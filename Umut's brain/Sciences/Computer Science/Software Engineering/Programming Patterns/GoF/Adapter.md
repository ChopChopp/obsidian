## Core Principal

Allow incompatible interfaces to work together by wrapping an existing class with a new interface. Acts as a bridge between two incompatible interfaces without modifying their source code.

## The Problem It Solves

- Need to use an existing class with an incompatible interface
- Want to integrate third-party libraries with different interfaces
- Legacy code needs to work with new systems
- Cannot modify existing classes due to constraints

## The Solution

Create an adapter class that implements the target interface and wraps the incompatible class (adaptee). The adapter translates calls from the target interface to the adaptee's interface.

## Quick Test

Ask yourself: "Do I have two classes that should work together but have incompatible interfaces?" If you can't modify either class and need them to collaborate, use Adapter.

## Examples

### Bad

```java
// Incompatible interfaces - can't work together directly
class OldPrinter {
    public void printOldFormat(String text) {
        System.out.println("Old format: " + text);
    }
}

class ModernSystem {
    public void processDocument() {
        // We want to use OldPrinter but interfaces don't match
        // OldPrinter printer = new OldPrinter();
        // printer.print("document"); // This method doesn't exist!
        
        // We're forced to call the old method directly
        OldPrinter printer = new OldPrinter();
        printer.printOldFormat("document"); // Breaks abstraction
    }
}

// Client expects this interface
interface Printer {
    void print(String document);
}
```

### Good

```java
// Legacy class with incompatible interface
class OldPrinter {
    public void printOldFormat(String text) {
        System.out.println("Old format: " + text);
    }
}

// Target interface that clients expect
interface Printer {
    void print(String document);
}

// Adapter makes OldPrinter compatible with Printer interface
class PrinterAdapter implements Printer {
    private OldPrinter oldPrinter;
    
    public PrinterAdapter(OldPrinter oldPrinter) {
        this.oldPrinter = oldPrinter;
    }
    
    @Override
    public void print(String document) {
        // Translate the interface call
        oldPrinter.printOldFormat(document);
    }
}

// Client code works with the standard interface
class ModernSystem {
    private Printer printer;
    
    public ModernSystem(Printer printer) {
        this.printer = printer;
    }
    
    public void processDocument() {
        printer.print("Important document");
    }
}

// Usage - OldPrinter now works seamlessly
OldPrinter legacyPrinter = new OldPrinter();
Printer adaptedPrinter = new PrinterAdapter(legacyPrinter);
ModernSystem system = new ModernSystem(adaptedPrinter);
system.processDocument();
```