# Core principal
Create classes that don't represent real-world domain concepts but are artifically designed to achieve low coupling, high cohesion and support reusability when applying other GRASP patterns would violate these principles.

# The problem it solves
**High coupling** when domain objects must handle technical concerns
**Low cohesion** when domain classes are forced to include unrelated functionality
**Violating other GRASP patterns** leads to poor design
**Domain pollution** with technical infrastructure concerns
**Difficult reusability** of technical services across different domains

# The solution
Introduce artificial classes that:
- Don't correspond to real-world domain concepts
- Handle purely technical or infrastructural concerns
- Provide services that support domain objects
- Maintain separation between business and technical logic
- Enable better adherence to other GRASP principles
- Act as utility or service classes

# Quick test
Ask "*Does this responsibility naturally belong to a real-world concept, or is it purely technical?*" If purely technical, consider a fabricated class.

# Examples
> Bad

``` java
// Customer domain object polluted with technical concerns
public class Customer {
    private String name;
    private String email;
    
    // Domain logic - appropriate
    public boolean isEligibleForDiscount() {
        return loyaltyPoints > 100;
    }
    
    // Technical concerns - violates pure domain
    public void saveToDatabase() {
        Connection conn = DriverManager.getConnection(...);
        PreparedStatement stmt = conn.prepareStatement(...);
        // Database logic here
    }
    
    public String toXML() {
        // XML serialization logic
        return "<customer><name>" + name + "</name>...</customer>";
    }
    
    public void logActivity(String action) {
        Logger.getLogger().info("Customer " + name + " performed: " + action);
    }
}
```

> Good

``` java
// Pure domain object
public class Customer {
    private String name;
    private String email;
    private int loyaltyPoints;
    
    // Only domain logic
    public boolean isEligibleForDiscount() {
        return loyaltyPoints > 100;
    }
    
    public void addLoyaltyPoints(int points) {
        this.loyaltyPoints += points;
    }
}

// Pure Fabrication - doesn't exist in real world
public class CustomerRepository {
    public void save(Customer customer) {
        Connection conn = getConnection();
        // Database persistence logic
    }
    
    public Customer findById(int id) {
        // Database retrieval logic
    }
}

// Pure Fabrication - technical service
public class XMLSerializer {
    public String serialize(Object obj) {
        // Generic XML serialization logic
        // Can be reused for any domain object
    }
}

// Pure Fabrication - infrastructure service  
public class ActivityLogger {
    public void logCustomerAction(Customer customer, String action) {
        Logger.getLogger().info("Customer " + customer.getName() + 
                              " performed: " + action);
    }
}

// Usage - clean separation
public class CustomerService {
    private CustomerRepository repository = new CustomerRepository();
    private ActivityLogger logger = new ActivityLogger();
    
    public void processCustomerOrder(Customer customer, Order order) {
        customer.addLoyaltyPoints(order.getPoints());
        repository.save(customer);
        logger.logCustomerAction(customer, "ORDER_PLACED");
    }
}
```

# Key Examples of Pure Fabrications
- Database access objects (DAOs/Repositories)
- Serialization services
- Logging utilities
- Configuration managers
- Format converters
- Algorithm implementations
- Factory classes
- Service facades