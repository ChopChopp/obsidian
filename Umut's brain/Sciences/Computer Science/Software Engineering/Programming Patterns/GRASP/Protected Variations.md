# Core principal
Identify points of predicted variation or instability and assign responsibilities to create a stable interface around them, protecting other elements from the impact of change.

# The problem it solves
- **Ripple effects** when changes in one part of the system break multiple other parts
- **Tight coupling** to unstable or frequently changing elements
- **Difficulty adapting** to new requirements or external system changes
- **Vendor lock-in** when code is tightly bound to specific implementations
- **Fragile architecture** that breaks easily when dependencies change
- **High maintenance costs** due to widespread impact of changes

# The solution
Create protective barriers by:
- Wrapping unstable elements behind stable interfaces
- Using abstraction layers to hide implementation details
- Applying design patterns that isolate variations
- Defining contracts that remain stable even when implementations change
- Anticipating likely points of variations and designing for them
- Creating plug-in architectures for extensibility

# Quick test
Ask: "*If this external dependency or requirement changed, how many parts of my system would break?*" If the answer is "many", you need protected variations.

# Difference to [[Polymorphism]]

**Polymorphism:**
- **Primary Goal:** Eliminate conditional logic based on type
- **Focus:** Handle existing type variations elegantly
- **When to Use:** You have multiple types that need different behavior

**Protected Variations:**
- **Primary Goal:** Shield system from future changes and instability
- **Focus:** Anticipate and protect against predicted variations
- **When to Use:** You expect dependencies or requirements to change

# Examples
> Bad

``` java
// Direct dependency on specific database technology
public class CustomerService {
    public void saveCustomer(Customer customer) {
        // Tightly coupled to MySQL
        Connection conn = DriverManager.getConnection(
            "jdbc:mysql://localhost:3306/mydb", "user", "pass");
        PreparedStatement stmt = conn.prepareStatement(
            "INSERT INTO customers (name, email) VALUES (?, ?)");
        stmt.setString(1, customer.getName());
        stmt.setString(2, customer.getEmail());
        stmt.executeUpdate();
        conn.close();
    }
    
    // Direct dependency on specific email service
    public void sendWelcomeEmail(Customer customer) {
        // Tightly coupled to SendGrid API
        SendGrid sg = new SendGrid("API_KEY");
        Email email = new Email();
        email.setTo(customer.getEmail());
        email.setSubject("Welcome!");
        email.setContent("Welcome to our service!");
        sg.send(email);
    }
    
    // Direct dependency on specific payment processor
    public void processPayment(Payment payment) {
        // Tightly coupled to Stripe
        Stripe.apiKey = "sk_test_...";
        Map<String, Object> params = new HashMap<>();
        params.put("amount", payment.getAmount());
        params.put("currency", "usd");
        params.put("source", payment.getToken());
        Charge.create(params);
    }
}
```

> Good

``` java
// Stable interfaces that protect against variations
public interface CustomerRepository {
    void save(Customer customer);
    Customer findById(Long id);
    List<Customer> findByEmail(String email);
}

public interface EmailService {
    void sendEmail(String to, String subject, String body);
    void sendWelcomeEmail(Customer customer);
}

public interface PaymentProcessor {
    PaymentResult processPayment(Payment payment);
    void refundPayment(String transactionId);
}

// Service layer protected from implementation changes
public class CustomerService {
    private final CustomerRepository repository;
    private final EmailService emailService;
    private final PaymentProcessor paymentProcessor;
    
    // Dependency injection - can swap implementations
    public CustomerService(CustomerRepository repository, 
                          EmailService emailService,
                          PaymentProcessor paymentProcessor) {
        this.repository = repository;
        this.emailService = emailService;
        this.paymentProcessor = paymentProcessor;
    }
    
    public void registerCustomer(Customer customer, Payment payment) {
        // Business logic remains stable regardless of implementation changes
        repository.save(customer);
        emailService.sendWelcomeEmail(customer);
        paymentProcessor.processPayment(payment);
    }
}

// Concrete implementations can be swapped without affecting service layer
public class MySQLCustomerRepository implements CustomerRepository {
    @Override
    public void save(Customer customer) {
        // MySQL-specific implementation
    }
    // ... other methods
}

public class PostgreSQLCustomerRepository implements CustomerRepository {
    @Override
    public void save(Customer customer) {
        // PostgreSQL-specific implementation
    }
    // ... other methods
}

public class SendGridEmailService implements EmailService {
    @Override
    public void sendEmail(String to, String subject, String body) {
        // SendGrid implementation
    }
    
    @Override
    public void sendWelcomeEmail(Customer customer) {
        sendEmail(customer.getEmail(), "Welcome!", "Welcome to our service!");
    }
}

public class AmazonSESEmailService implements EmailService {
    @Override
    public void sendEmail(String to, String subject, String body) {
        // Amazon SES implementation
    }
    
    @Override
    public void sendWelcomeEmail(Customer customer) {
        sendEmail(customer.getEmail(), "Welcome!", "Welcome to our service!");
    }
}

// Configuration/Factory to wire up implementations
public class ServiceConfiguration {
    public CustomerService createCustomerService() {
        CustomerRepository repo = new MySQLCustomerRepository();
        EmailService emailSvc = new SendGridEmailService();
        PaymentProcessor paymentProc = new StripePaymentProcessor();
        
        return new CustomerService(repo, emailSvc, paymentProc);
    }
    
    // Easy to create different configurations for testing
    public CustomerService createTestCustomerService() {
        CustomerRepository repo = new InMemoryCustomerRepository();
        EmailService emailSvc = new MockEmailService();
        PaymentProcessor paymentProc = new MockPaymentProcessor();
        
        return new CustomerService(repo, emailSvc, paymentProc);
    }
}
```
