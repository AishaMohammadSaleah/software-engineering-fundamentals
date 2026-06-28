## 5. Dependency Inversion Principle (DIP)

**Definition:**
The Dependency Inversion Principle states that high-level modules must not depend on low-level modules directly. We must add a mediator between them to ensure they are completely decoupled, so any change on the low level doesn't affect the high level. As the principle says: *"High-level modules should not depend on low-level modules. Both should depend on abstractions."*

**Example & Violation:**
Imagine we have a high-level module called `Project` that directly connects to a specific low-level database class like `MySQLDatabase`:

```cpp
class MySQLDatabase {
public:
    void connect() { /* Connects to MySQL */ }
};

// Violation: High level depends directly on low level
class Project {
    MySQLDatabase db; // Hardcoded dependency
public:
    void start() {
        db.connect(); 
    }
};
``` 
If the manager decides to change the database type in the future, the programmer will be forced to modify the Project class code because it depends directly on MySQL. Any change in the low level breaks the high level.

The Solution:
To enforce DIP and decouple them, we add an interface as a Mediator between the high level and the low level. Both will now depend on this abstraction.
```cpp
// The Mediator (Abstraction)
class IDatabase {
public:
    virtual void connect() = 0;
};

// High level depends only on the Mediator
class Project {
    IDatabase* db; 
public:
    void start() {
        db->connect(); // Decoupled!
    }
};

// Low level implements the Mediator
class MySQLDatabase : public IDatabase {
public:
    void connect() override { /* MySQL connection stuff */ }
};

class MongoDBDatabase : public IDatabase {
public:
    void connect() override { /* MongoDB connection stuff */ }
};
```
Now, the low level and the high level are decoupled. If we add or change any database type, the high-level Project remains completely untouched and safe.