## Open-Closed Principle (OCP)

**Definition:**
Software entities (classes, modules, functions, etc.) should be open for extension but closed for modification. This means you should be able to introduce new production-ready features or extend the system's behavior without altering or breaking the existing, already-tested source code.

**Example & Violation:**
Imagine building an application that currently relies on only one type of database (e.g., MySQL). If the database connection logic is hardcoded directly into the system, what happens when the manager decides to switch to MongoDB, or add PostgreSQL as an alternative? 

The programmer will be forced to go back and modify all the existing code to accommodate the new database requirements. If the manager requests further changes in the future, the team will end up trapped in an infinite loop of rewriting, changing, and refactoring everything. At some point, the system will become too fragile to maintain and completely incapable of scale.



**The Solution:**
To enforce OCP and ensure scalability, we use **Abstraction** (either through interfaces or abstract classes). 

Instead of depending on a concrete database class, we create a unified `DatabaseConnection` interface. Every specific database type (e.g., `MySQLConnection`, `MongoDBConnection`) will then implement this interface and handle its own inner logic. 

```cpp
// High-level abstraction
class DatabaseConnection {
public:
    virtual void connect() = 0;
};

// Open for extension: easily adding new types without modifying the core system
class MySQLConnection : public DatabaseConnection {
public:
    void connect() override { /* MySQL specific logic */ }
};

class MongoDBConnection : public DatabaseConnection {
public:
    void connect() override { /* MongoDB specific logic */ }
};

