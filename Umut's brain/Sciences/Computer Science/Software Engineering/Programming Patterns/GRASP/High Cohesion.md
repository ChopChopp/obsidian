# Core principal
A design principle that ensures each class has a single, well-defined responsibility where all elements work together towards a common, focused purpose.

# The problem it solves
- **Scattered functionality** across classes with unclear purpose
- **Hard-to-understand code** where classes do too many unrelated things
- **Difficult maintenance** when changes affect multiple unrelated aspects
- **Poor reusability** of classes that mix different concerns
- **Complex testing** due to multiple responsibilities in one class
- **Violation of Single Responsibility Principle** leading to fragile code

# The solution
Desgin classes where:
- All methods and attributes relate to a single, cohesive purpose
- The class name clearly indicates its responsibility
- Methods work together to fulfill the class's primary role
- Related functionality is grouped together
- Unrelated concerns are seperated into different classes

# Quick test
Can you describe what your class does in one clear sentence without using "and" or "or"?
If not, it likely has low cohesion and needs to be split.

# Examples
> Bad

``` java
// Class doing too many unrelated things
public class CustomerHandler {
    // Database operations
    public void saveCustomer(Customer c) { ... }
    public Customer findCustomer(int id) { ... }
    
    // Email operations  
    public void sendWelcomeEmail(Customer c) { ... }
    public void sendPromotions(List<Customer> customers) { ... }
    
    // Report generation
    public Report generateCustomerReport() { ... }
    public void exportToCSV(List<Customer> customers) { ... }
    
    // Validation
    public boolean validateEmail(String email) { ... }
    public boolean validateAge(int age) { ... }
}
```

> Good

``` java
// Focused on customer data persistence
public class CustomerRepository {
    public void save(Customer customer) { ... }
    public Customer findById(int id) { ... }
    public List<Customer> findByCity(String city) { ... }
    public void delete(int customerId) { ... }
}

// Focused on customer communication
public class CustomerEmailService {
    public void sendWelcomeEmail(Customer customer) { ... }
    public void sendPromotions(List<Customer> customers) { ... }
    public void sendPasswordReset(Customer customer) { ... }
}

// Focused on customer data validation
public class CustomerValidator {
    public ValidationResult validate(Customer customer) { ... }
    public boolean isValidEmail(String email) { ... }
    public boolean isValidAge(int age) { ... }
}

// Focused on customer reporting
public class CustomerReportGenerator {
    public Report generateSummaryReport() { ... }
    public void exportToCSV(List<Customer> customers) { ... }
    public Report generateActivityReport(int customerId) { ... }
}
```
