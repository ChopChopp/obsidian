## Core Principal

Encapsulate object creation logic in a separate factory class with a static method that returns objects based on input parameters. Centralizes object creation and hides instantiation details from clients.

## The Problem It Solves

- Object creation logic is scattered throughout the codebase
- Clients are tightly coupled to concrete classes
- Need to centralize creation logic for easier maintenance
- Want to hide complex instantiation logic from clients

## The Solution

Create a factory class with a static method that takes parameters and returns the appropriate object. All object creation logic is centralized in the factory, making it easier to maintain and modify.

## Quick Test

Ask yourself: "Do I have object creation logic scattered in multiple places that could be centralized?" If you want to hide instantiation details and centralize creation logic, use Simple Factory.

## Examples

### Bad

```java
// Object creation scattered throughout the application
class OrderProcessor {
    public void processPayment(String paymentType, double amount) {
        PaymentProcessor processor;
        
        // Creation logic duplicated everywhere it's needed
        if (paymentType.equals("CREDIT_CARD")) {
            processor = new CreditCardProcessor("visa", "1234-5678-9012-3456");
        } else if (paymentType.equals("PAYPAL")) {
            processor = new PayPalProcessor("user@example.com", "password123");
        } else if (paymentType.equals("BANK_TRANSFER")) {
            processor = new BankTransferProcessor("12345", "67890");
        } else {
            throw new IllegalArgumentException("Unknown payment type");
        }
        
        processor.processPayment(amount);
    }
}

class ShoppingCart {
    public void checkout(String paymentType, double total) {
        PaymentProcessor processor;
        
        // Same creation logic duplicated again!
        if (paymentType.equals("CREDIT_CARD")) {
            processor = new CreditCardProcessor("visa", "1234-5678-9012-3456");
        } else if (paymentType.equals("PAYPAL")) {
            processor = new PayPalProcessor("user@example.com", "password123");
        } else if (paymentType.equals("BANK_TRANSFER")) {
            processor = new BankTransferProcessor("12345", "67890");
        } else {
            throw new IllegalArgumentException("Unknown payment type");
        }
        
        processor.processPayment(total);
    }
}

interface PaymentProcessor {
    void processPayment(double amount);
}

class CreditCardProcessor implements PaymentProcessor {
    private String cardType, cardNumber;
    public CreditCardProcessor(String cardType, String cardNumber) {
        this.cardType = cardType;
        this.cardNumber = cardNumber;
    }
    public void processPayment(double amount) {
        System.out.println("Processing $" + amount + " via " + cardType);
    }
}

class PayPalProcessor implements PaymentProcessor {
    private String email, password;
    public PayPalProcessor(String email, String password) {
        this.email = email;
        this.password = password;
    }
    public void processPayment(double amount) {
        System.out.println("Processing $" + amount + " via PayPal");
    }
}

class BankTransferProcessor implements PaymentProcessor {
    private String accountNumber, routingNumber;
    public BankTransferProcessor(String accountNumber, String routingNumber) {
        this.accountNumber = accountNumber;
        this.routingNumber = routingNumber;
    }
    public void processPayment(double amount) {
        System.out.println("Processing $" + amount + " via bank transfer");
    }
}
```

### Good

```java
// Product interface
interface PaymentProcessor {
    void processPayment(double amount);
}

// Concrete products
class CreditCardProcessor implements PaymentProcessor {
    private String cardType, cardNumber;
    
    public CreditCardProcessor(String cardType, String cardNumber) {
        this.cardType = cardType;
        this.cardNumber = cardNumber;
    }
    
    @Override
    public void processPayment(double amount) {
        System.out.println("Processing $" + amount + " via " + cardType + " ending in " + 
                          cardNumber.substring(cardNumber.length() - 4));
    }
}

class PayPalProcessor implements PaymentProcessor {
    private String email;
    
    public PayPalProcessor(String email) {
        this.email = email;
    }
    
    @Override
    public void processPayment(double amount) {
        System.out.println("Processing $" + amount + " via PayPal account: " + email);
    }
}

class BankTransferProcessor implements PaymentProcessor {
    private String accountNumber;
    
    public BankTransferProcessor(String accountNumber) {
        this.accountNumber = accountNumber;
    }
    
    @Override
    public void processPayment(double amount) {
        System.out.println("Processing $" + amount + " via bank transfer to account: " + accountNumber);
    }
}

// Simple Factory - centralizes object creation
class PaymentProcessorFactory {
    public static PaymentProcessor createProcessor(String type, String... params) {
        switch (type.toUpperCase()) {
            case "CREDIT_CARD":
                if (params.length < 2) {
                    throw new IllegalArgumentException("Credit card requires cardType and cardNumber");
                }
                return new CreditCardProcessor(params[0], params[1]);
                
            case "PAYPAL":
                if (params.length < 1) {
                    throw new IllegalArgumentException("PayPal requires email");
                }
                return new PayPalProcessor(params[0]);
                
            case "BANK_TRANSFER":
                if (params.length < 1) {
                    throw new IllegalArgumentException("Bank transfer requires account number");
                }
                return new BankTransferProcessor(params[0]);
                
            default:
                throw new IllegalArgumentException("Unknown payment type: " + type);
        }
    }
}

// Client code is simplified and decoupled
class OrderProcessor {
    public void processPayment(String paymentType, double amount) {
        PaymentProcessor processor = PaymentProcessorFactory.createProcessor(
            paymentType, "visa", "1234-5678-9012-3456"
        );
        processor.processPayment(amount);
    }
}

class ShoppingCart {
    public void checkout(String paymentType, double total) {
        PaymentProcessor processor = PaymentProcessorFactory.createProcessor(
            paymentType, "user@example.com"
        );
        processor.processPayment(total);
    }
}

// Usage - clean and centralized
PaymentProcessor creditProcessor = PaymentProcessorFactory.createProcessor(
    "CREDIT_CARD", "mastercard", "9876-5432-1098-7654"
);
PaymentProcessor paypalProcessor = PaymentProcessorFactory.createProcessor(
    "PAYPAL", "customer@email.com"
);

creditProcessor.processPayment(100.0);
paypalProcessor.processPayment(50.0);
```