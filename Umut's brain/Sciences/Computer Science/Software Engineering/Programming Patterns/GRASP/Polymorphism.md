# Core principal
Assign responsibilities to types and objects so that behavior varies by type, using polymorphic operations instead of conditional logic to handle type-based variations.

# The problem it solves
- **Excessive conditional statements** based on type checking (if-else chains, switch statements)
- **Rigid code** that's difficult to extend with new types
- **Violation of Open-Closed principle** when adding new variations required modifying existing code
- **Code duplication** in similar conditional structures
- **Poor maintainability** when type-specific logic is scattered
- **Tight coupling** between client code and specific implementations

# The solution
Use polymorphism to:
- Define common interfaces or abstract classes for related types
- Implement type-specific behavior in each concrete class
- Let clients work with abstractions rather than concrete types
- Enable easy extension with new types without modifying existing code

# Quick test
If you find yourself writing `if (object.getType() == ...)` or similar type-checking conditionals, consider using polymorphism instead.

# Examples
> Bad

``` java
public class PaymentProcessor {
    public void processPayment(Payment payment) {
        if (payment.getType() == PaymentType.CREDIT_CARD) {
            CreditCard cc = (CreditCard) payment;
            // Credit card specific logic
            validateCreditCard(cc.getNumber());
            chargeCreditCard(cc.getNumber(), payment.getAmount());
            sendCreditCardReceipt(cc.getEmail());
        } 
        else if (payment.getType() == PaymentType.PAYPAL) {
            PayPal pp = (PayPal) payment;
            // PayPal specific logic
            validatePayPalAccount(pp.getAccountId());
            chargePayPal(pp.getAccountId(), payment.getAmount());
            sendPayPalReceipt(pp.getEmail());
        }
        else if (payment.getType() == PaymentType.BANK_TRANSFER) {
            BankTransfer bt = (BankTransfer) payment;
            // Bank transfer specific logic
            validateBankAccount(bt.getAccountNumber());
            initiateBankTransfer(bt.getAccountNumber(), payment.getAmount());
            sendBankTransferReceipt(bt.getEmail());
        }
        // Adding new payment types requires modifying this method
    }
    
    public double calculateFee(Payment payment) {
        if (payment.getType() == PaymentType.CREDIT_CARD) {
            return payment.getAmount() * 0.03; // 3% fee
        } 
        else if (payment.getType() == PaymentType.PAYPAL) {
            return payment.getAmount() * 0.025; // 2.5% fee
        }
        else if (payment.getType() == PaymentType.BANK_TRANSFER) {
            return 5.00; // Fixed $5 fee
        }
        return 0;
    }
}
```

> Good

``` java
// Common interface defining polymorphic operations
public abstract class Payment {
    protected double amount;
    protected String email;
    
    // Template method using polymorphism
    public final void process() {
        validate();
        executePayment();
        sendReceipt();
    }
    
    // Polymorphic methods - behavior varies by type
    protected abstract void validate();
    protected abstract void executePayment();
    protected abstract void sendReceipt();
    public abstract double calculateFee();
}

// Concrete implementations with type-specific behavior
public class CreditCardPayment extends Payment {
    private String cardNumber;
    
    @Override
    protected void validate() {
        validateCreditCard(cardNumber);
    }
    
    @Override
    protected void executePayment() {
        chargeCreditCard(cardNumber, amount);
    }
    
    @Override
    protected void sendReceipt() {
        sendCreditCardReceipt(email);
    }
    
    @Override
    public double calculateFee() {
        return amount * 0.03; // 3% fee
    }
}

public class PayPalPayment extends Payment {
    private String accountId;
    
    @Override
    protected void validate() {
        validatePayPalAccount(accountId);
    }
    
    @Override
    protected void executePayment() {
        chargePayPal(accountId, amount);
    }
    
    @Override
    protected void sendReceipt() {
        sendPayPalReceipt(email);
    }
    
    @Override
    public double calculateFee() {
        return amount * 0.025; // 2.5% fee
    }
}

public class BankTransferPayment extends Payment {
    private String accountNumber;
    
    @Override
    protected void validate() {
        validateBankAccount(accountNumber);
    }
    
    @Override
    protected void executePayment() {
        initiateBankTransfer(accountNumber, amount);
    }
    
    @Override
    protected void sendReceipt() {
        sendBankTransferReceipt(email);
    }
    
    @Override
    public double calculateFee() {
        return 5.00; // Fixed $5 fee
    }
}

// Clean client code - no conditionals needed
public class PaymentProcessor {
    public void processPayment(Payment payment) {
        payment.process(); // Polymorphic call
    }
    
    public double calculateTotalWithFee(Payment payment) {
        return payment.getAmount() + payment.calculateFee(); // Polymorphic call
    }
}

// Easy to extend - adding new payment types doesn't require changing existing code
public class CryptocurrencyPayment extends Payment {
    private String walletAddress;
    
    @Override
    protected void validate() {
        validateWallet(walletAddress);
    }
    
    @Override
    protected void executePayment() {
        transferCrypto(walletAddress, amount);
    }
    
    @Override
    protected void sendReceipt() {
        sendCryptoReceipt(email);
    }
    
    @Override
    public double calculateFee() {
        return amount * 0.01; // 1% fee
    }
}
```
