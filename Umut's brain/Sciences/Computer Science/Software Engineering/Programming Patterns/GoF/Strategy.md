## Core Principal

Define a family of algorithms, encapsulate each one, and make them interchangeable. Strategy lets the algorithm vary independently from clients that use it, promoting composition over inheritance.

## The Problem It Solves

- Multiple ways to perform the same task (different algorithms)
- Need to switch algorithms at runtime
- Want to avoid large conditional statements for algorithm selection
- Need to add new algorithms without modifying existing code

## The Solution

Create a strategy interface for the algorithm family, implement concrete strategies for each algorithm, and use composition in the context class to delegate to the current strategy.

## Quick Test

Ask yourself: "Do I have multiple ways to accomplish the same task and want to switch between them dynamically?" If you need interchangeable algorithms, use Strategy.

## Examples

### Bad

```java
// Algorithm selection with conditionals - hard to extend
class PaymentProcessor {
    public static final String CREDIT_CARD = "CREDIT_CARD";
    public static final String PAYPAL = "PAYPAL";
    public static final String BANK_TRANSFER = "BANK_TRANSFER";
    public static final String CRYPTOCURRENCY = "CRYPTOCURRENCY";
    
    private String paymentMethod;
    
    public PaymentProcessor(String paymentMethod) {
        this.paymentMethod = paymentMethod;
    }
    
    public void processPayment(double amount) {
        // Large conditional statement - violates Open/Closed Principle
        if (paymentMethod.equals(CREDIT_CARD)) {
            System.out.println("Processing credit card payment:");
            System.out.println("- Validating card number");
            System.out.println("- Checking credit limit");
            System.out.println("- Charging $" + amount);
            System.out.println("- Sending confirmation");
        } else if (paymentMethod.equals(PAYPAL)) {
            System.out.println("Processing PayPal payment:");
            System.out.println("- Redirecting to PayPal");
            System.out.println("- Authenticating user");
            System.out.println("- Transferring $" + amount);
            System.out.println("- Returning to merchant");
        } else if (paymentMethod.equals(BANK_TRANSFER)) {
            System.out.println("Processing bank transfer:");
            System.out.println("- Verifying account details");
            System.out.println("- Initiating transfer of $" + amount);
            System.out.println("- Waiting for confirmation");
        } else if (paymentMethod.equals(CRYPTOCURRENCY)) {
            System.out.println("Processing cryptocurrency payment:");
            System.out.println("- Generating wallet address");
            System.out.println("- Waiting for " + amount + " confirmation");
            System.out.println("- Verifying blockchain transaction");
        } else {
            throw new IllegalArgumentException("Unsupported payment method");
        }
        // Adding new payment methods requires modifying this method!
    }
    
    public double calculateFee(double amount) {
        // More conditionals for different fee structures
        if (paymentMethod.equals(CREDIT_CARD)) {
            return amount * 0.029; // 2.9%
        } else if (paymentMethod.equals(PAYPAL)) {
            return amount * 0.034; // 3.4%
        } else if (paymentMethod.equals(BANK_TRANSFER)) {
            return 5.0; // Flat $5 fee
        } else if (paymentMethod.equals(CRYPTOCURRENCY)) {
            return amount * 0.01; // 1%
        } else {
            return 0;
        }
    }
}
```

### Good

```java
// Strategy interface
interface PaymentStrategy {
    void processPayment(double amount);
    double calculateFee(double amount);
}

// Concrete strategies
class CreditCardStrategy implements PaymentStrategy {
    private String cardNumber;
    private String cardHolder;
    
    public CreditCardStrategy(String cardNumber, String cardHolder) {
        this.cardNumber = cardNumber;
        this.cardHolder = cardHolder;
    }
    
    @Override
    public void processPayment(double amount) {
        System.out.println("Processing credit card payment:");
        System.out.println("- Validating card number: " + maskCardNumber());
        System.out.println("- Checking credit limit for " + cardHolder);
        System.out.println("- Charging $" + amount);
        System.out.println("- Sending confirmation");
    }
    
    @Override
    public double calculateFee(double amount) {
        return amount * 0.029; // 2.9%
    }
    
    private String maskCardNumber() {
        return "**** **** **** " + cardNumber.substring(cardNumber.length() - 4);
    }
}

class PayPalStrategy implements PaymentStrategy {
    private String email;
    
    public PayPalStrategy(String email) {
        this.email = email;
    }
    
    @Override
    public void processPayment(double amount) {
        System.out.println("Processing PayPal payment:");
        System.out.println("- Redirecting to PayPal for " + email);
        System.out.println("- Authenticating user");
        System.out.println("- Transferring $" + amount);
        System.out.println("- Returning to merchant");
    }
    
    @Override
    public double calculateFee(double amount) {
        return amount * 0.034; // 3.4%
    }
}

class BankTransferStrategy implements PaymentStrategy {
    private String accountNumber;
    private String routingNumber;
    
    public BankTransferStrategy(String accountNumber, String routingNumber) {
        this.accountNumber = accountNumber;
        this.routingNumber = routingNumber;
    }
    
    @Override
    public void processPayment(double amount) {
        System.out.println("Processing bank transfer:");
        System.out.println("- Verifying account: " + maskAccount());
        System.out.println("- Initiating transfer of $" + amount);
        System.out.println("- Waiting for confirmation");
    }
    
    @Override
    public double calculateFee(double amount) {
        return 5.0; // Flat $5 fee
    }
    
    private String maskAccount() {
        return "****" + accountNumber.substring(accountNumber.length() - 4);
    }
}

class CryptocurrencyStrategy implements PaymentStrategy {
    private String walletAddress;
    private String coinType;
    
    public CryptocurrencyStrategy(String walletAddress, String coinType) {
        this.walletAddress = walletAddress;
        this.coinType = coinType;
    }
    
    @Override
    public void processPayment(double amount) {
        System.out.println("Processing " + coinType + " payment:");
        System.out.println("- Generating wallet address: " + walletAddress);
        System.out.println("- Waiting for " + amount + " " + coinType + " confirmation");
        System.out.println("- Verifying blockchain transaction");
    }
    
    @Override
    public double calculateFee(double amount) {
        return amount * 0.01; // 1%
    }
}

// Context class uses composition instead of inheritance
class PaymentProcessor {
    private PaymentStrategy strategy;
    
    public PaymentProcessor(PaymentStrategy strategy) {
        this.strategy = strategy;
    }
    
    // Can change strategy at runtime
    public void setStrategy(PaymentStrategy strategy) {
        this.strategy = strategy;
    }
    
    public void processPayment(double amount) {
        double fee = strategy.calculateFee(amount);
        System.out.println("Processing payment of $" + amount + " (fee: $" + fee + ")");
        strategy.processPayment(amount);
        System.out.println("Payment completed!\n");
    }
    
    public double calculateTotalCost(double amount) {
        return amount + strategy.calculateFee(amount);
    }
}

// Usage - flexible and extensible
PaymentProcessor processor = new PaymentProcessor(
    new CreditCardStrategy("1234-5678-9012-3456", "John Doe")
);
processor.processPayment(100.0);

// Switch strategies at runtime
processor.setStrategy(new PayPalStrategy("john@example.com"));
processor.processPayment(50.0);

processor.setStrategy(new BankTransferStrategy("987654321", "123456789"));
processor.processPayment(200.0);

processor.setStrategy(new CryptocurrencyStrategy("1A1zP1eP5QGefi2DMPTfTL5SLmv7DivfNa", "Bitcoin"));
processor.processPayment(75.0);

// Adding new payment methods only requires implementing PaymentStrategy
// No modification of existing code needed!
```