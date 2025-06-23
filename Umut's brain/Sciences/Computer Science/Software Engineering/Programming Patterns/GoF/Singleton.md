## Core Principal

Ensure a class has only one instance and provide a global point of access to that instance. Controls object creation to guarantee only one instance exists throughout the application lifecycle.

## The Problem It Solves

- Need exactly one instance of a class (database connections, loggers, caches)
- Want to control access to shared resources
- Need global access to an instance without global variables
- Want to lazy-load expensive resources

## The Solution

Make the constructor private, provide a static method to get the instance, and store the single instance as a static field. The class controls its own instantiation and ensures only one instance exists.

## Quick Test

Ask yourself: "Should there be exactly one instance of this class in my entire application?" If the answer is yes and you need global access, consider Singleton (but be aware of its drawbacks).

## Examples

### Bad

```java
// Multiple instances of what should be a single resource
class DatabaseConnection {
    private String connectionString;
    
    public DatabaseConnection() {
        this.connectionString = "jdbc:mysql://localhost:3306/mydb";
        connect();
    }
    
    private void connect() {
        System.out.println("Connecting to database...");
        // Expensive connection setup
    }
    
    public void query(String sql) {
        System.out.println("Executing: " + sql);
    }
}

class UserService {
    private DatabaseConnection db;
    
    public UserService() {
        // Creates a new connection every time!
        this.db = new DatabaseConnection();
    }
    
    public void getUser(int id) {
        db.query("SELECT * FROM users WHERE id = " + id);
    }
}

class OrderService {
    private DatabaseConnection db;
    
    public OrderService() {
        // Another connection created!
        this.db = new DatabaseConnection();
    }
    
    public void getOrder(int id) {
        db.query("SELECT * FROM orders WHERE id = " + id);
    }
}

// Usage creates multiple database connections
UserService userService = new UserService();   // Connection 1
OrderService orderService = new OrderService(); // Connection 2
UserService anotherUser = new UserService();    // Connection 3
// This wastes resources and may cause connection pool issues!
```

### Good

```java
// Thread-safe Singleton implementation
class DatabaseConnection {
    // Volatile ensures visibility across threads
    private static volatile DatabaseConnection instance;
    private String connectionString;
    
    // Private constructor prevents external instantiation
    private DatabaseConnection() {
        this.connectionString = "jdbc:mysql://localhost:3306/mydb";
        connect();
    }
    
    // Thread-safe lazy initialization using double-checked locking
    public static DatabaseConnection getInstance() {
        if (instance == null) {
            synchronized (DatabaseConnection.class) {
                if (instance == null) {
                    instance = new DatabaseConnection();
                }
            }
        }
        return instance;
    }
    
    private void connect() {
        System.out.println("Connecting to database...");
        // Expensive connection setup happens only once
    }
    
    public void query(String sql) {
        System.out.println("Executing: " + sql);
    }
    
    // Prevent cloning
    @Override
    protected Object clone() throws CloneNotSupportedException {
        throw new CloneNotSupportedException("Singleton cannot be cloned");
    }
}

// Alternative: Enum Singleton (simpler and thread-safe by default)
enum DatabaseConnectionEnum {
    INSTANCE;
    
    private String connectionString;
    
    DatabaseConnectionEnum() {
        this.connectionString = "jdbc:mysql://localhost:3306/mydb";
        connect();
    }
    
    private void connect() {
        System.out.println("Enum Singleton: Connecting to database...");
    }
    
    public void query(String sql) {
        System.out.println("Enum Singleton executing: " + sql);
    }
}

// Services now share the same database connection
class UserService {
    private DatabaseConnection db;
    
    public UserService() {
        // Gets the same instance every time
        this.db = DatabaseConnection.getInstance();
    }
    
    public void getUser(int id) {
        db.query("SELECT * FROM users WHERE id = " + id);
    }
}

class OrderService {
    private DatabaseConnection db;
    
    public OrderService() {
        // Same instance as UserService
        this.db = DatabaseConnection.getInstance();
    }
    
    public void getOrder(int id) {
        db.query("SELECT * FROM orders WHERE id = " + id);
    }
}

// Usage - all services share the same connection
UserService userService1 = new UserService();
OrderService orderService = new OrderService();
UserService userService2 = new UserService();

// Verify they're the same instance
DatabaseConnection db1 = DatabaseConnection.getInstance();
DatabaseConnection db2 = DatabaseConnection.getInstance();
System.out.println("Same instance: " + (db1 == db2)); // true

// Enum singleton usage
DatabaseConnectionEnum.INSTANCE.query("SELECT 1");

// Note: While Singleton solves the multiple instance problem,
// it can make code harder to test and creates tight coupling.
// Consider dependency injection as an alternative when possible.
```