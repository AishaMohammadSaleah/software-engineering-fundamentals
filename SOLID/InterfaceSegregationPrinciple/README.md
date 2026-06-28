## 4. Interface Segregation Principle (ISP)

**Definition:**
The Interface Segregation Principle states that a client must only deal with interface methods that serve their specific needs, without being burdened by any further unnecessary stuff. It is essentially the Single Responsibility Principle (SRP) but applied specifically to interfaces. As the principle says: *"A client should never be forced to implement an interface that it doesn't use, or clients shouldn't be forced to depend on methods they do not use."*

**Example & Violation:**
Imagine designing a User Management system and creating a massive interface called `IUserRepository` to handle all operations for all types of users:

```cpp
class IUserRepository {
public:
    virtual void login() = 0;
    virtual void registerAccount() = 0;
    virtual void updateProfile() = 0;
    virtual void deleteUser() = 0; // Admin-only action
};
```
Now, we want to implement a class for a NormalUser. A regular user can log in, register, and update their profile, but they should never have the permission to delete other users. However, because NormalUser implements IUserRepository, the programmer is forced to override the deleteUser() method, leaving it empty or throwing a "Not Authorized" exception:
```cpp
class NormalUser : public IUserRepository {
public:
    void login() override { /* Login logic */ }
    void registerAccount() override { /* Registration logic */ }
    void updateProfile() override { /* Update profile logic */ }
    void deleteUser() override { 
        // Forcefully implemented! Regular users shouldn't even see this function.
        throw std::runtime_error("Access Denied"); 
    }
};
```
This heavily violates ISP because the NormalUser client is forced to depend on and implement an admin-level method that it shouldn't even know exists.

The Solution:
To enforce ISP, we must split the "fat interface" into smaller, specialized interfaces based on the user's role:
```cpp
class IBasicActions {
public:
    virtual void login() = 0;
    virtual void registerAccount() = 0;
    virtual void updateProfile() = 0;
};

class IAdminActions {
public:
    virtual void deleteUser() = 0;
};
```
Now, the NormalUser only implements IBasicActions, keeping its code clean and secure. If we need to create an AdminUser, it can implement both interfaces via multiple inheritance:
```cpp
class NormalUser : public IBasicActions {
public:
    void login() override { /* ... */ }
    void registerAccount() override { /* ... */ }
    void updateProfile() override { /* ... */ }
};

class AdminUser : public IBasicActions, public IAdminActions {
public:
    void login() override { /* ... */ }
    void registerAccount() override { /* ... */ }
    void updateProfile() override { /* ... */ }
    void deleteUser() override { /* Admin logic to delete a user */ }
};
```