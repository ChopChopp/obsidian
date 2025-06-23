# Core principal
Assign responsibility to an intermediate object to mediate between other components or services to avoid direct coupling, where direct coupling would be problematic.

# The problem it solves
- **Tight coupling** between objects that shouldn't know about each other directly
- **Difficult testing** when objects have directed dependencies on complex systems
- **Inflexible design** where changes in one object force changes in many others
- **Violation of separation of concerns** when objects handle responsibilities outside their domain
- **Hard-to-maintain code** with complex interdependencies
- **Reduced reusability** due to specific coupling requirements

# The solution
Introduce an intermediary object that:
- Acts as a go-between for objects that shouldn't be directly coupled
- Provides a level of abstraction between components
- Handles coordination, translation, or delegations of responsibilities
- Maintains loose coupling while enabling necessary interactions
- Can be easily substituted or modified without affecting clients

# Key signs that you need it
- Objects directly instantiating other objects they depend on
- Difficulty mocking dependencies for unit tests
- Changes in one class requiring changes in multiple other classes
- Objects performing tasks outside their primary responsibility
- Hard-coded dependencies on specific implementations
- Complex initialization or setup code throughout classes

# Quick test
If an object directly creates (`new`) another object it depends on, or directly calls external system, consider if indirection would improve the design.

# Examples
> Bad

``` java
public class OrderService {
    public void processOrder(Order order) {
        // Direct coupling to specific implementations
        // Hard-coded dependency
        EmailSender emailSender = new SMTPEmailSender(); 
        // Hard-coded dependency
        PaymentProcessor processor = new StripePaymentProcessor();
        // Hard-coded dependency
        InventorySystem inventory = new DatabaseInventorySystem();
        
        // Direct coupling to external systems
        Connection dbConn = DriverManager.getConnection(
            "jdbc:mysql://localhost:3306/orders", "user", "pass");
        
        // Business logic mixed with infrastructure concerns
        if (inventory.checkAvailability(order.getItems())) {
            PaymentResult result = processor.processPayment(order.getPayment());
            
            if (result.isSuccessful()) {
                // Direct database manipulation
                PreparedStatement stmt = dbConn.prepareStatement(
                    "INSERT INTO orders (customer_id, total) VALUES (?, ?)");
                stmt.setLong(1, order.getCustomerId());
                stmt.setBigDecimal(2, order.getTotal());
                stmt.executeUpdate();
                
                emailSender.sendConfirmation(order.getCustomerEmail(), order.getId());
            }
        }
        
        dbConn.close();
    }
}
```

> Good

``` java
// Indirection through interfaces
public interface EmailService {
    void sendOrderConfirmation(String email, String orderId);
}

public interface PaymentService {
    PaymentResult processPayment(PaymentInfo payment);
}

public interface InventoryService {
    boolean checkAvailability(List<OrderItem> items);
    void reserveItems(List<OrderItem> items);
}

public interface OrderRepository {
    void save(Order order);
    Order findById(String orderId);
}

// Clean service with injected dependencies (indirection)
public class OrderService {
    private final EmailService emailService;
    private final PaymentService paymentService;
    private final InventoryService inventoryService;
    private final OrderRepository orderRepository;
    
    // Constructor injection - indirection mechanism
    public OrderService(EmailService emailService,
                       PaymentService paymentService,
                       InventoryService inventoryService,
                       OrderRepository orderRepository) {
        this.emailService = emailService;
        this.paymentService = paymentService;
        this.inventoryService = inventoryService;
        this.orderRepository = orderRepository;
    }
    
    public void processOrder(Order order) {
        // Clean business logic - no direct coupling
        if (inventoryService.checkAvailability(order.getItems())) {
            PaymentResult result = paymentService.processPayment(order.getPayment());
            
            if (result.isSuccessful()) {
                inventoryService.reserveItems(order.getItems());
                orderRepository.save(order);
                emailService.sendOrderConfirmation(order.getCustomerEmail(), order.getId());
            }
        }
    }
}

// Factory/Configuration providing indirection for object creation
public class ServiceFactory {
    public OrderService createOrderService() {
        // Indirection - clients don't need to know about concrete implementations
        EmailService emailService = new SMTPEmailService();
        PaymentService paymentService = new StripePaymentService();
        InventoryService inventoryService = new DatabaseInventoryService();
        OrderRepository orderRepository = new JdbcOrderRepository();
        
        return new OrderService(emailService, paymentService, 
                              inventoryService, orderRepository);
    }
    
    // Easy to create different configurations
    public OrderService createTestOrderService() {
        EmailService emailService = new MockEmailService();
        PaymentService paymentService = new MockPaymentService();
        InventoryService inventoryService = new InMemoryInventoryService();
        OrderRepository orderRepository = new InMemoryOrderRepository();
        
        return new OrderService(emailService, paymentService, 
                              inventoryService, orderRepository);
    }
}
```
