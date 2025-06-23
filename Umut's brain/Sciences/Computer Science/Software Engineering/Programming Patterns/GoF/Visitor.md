## Core Principal

Represent an operation to be performed on elements of an object structure. Visitor lets you define new operations without changing the classes of the elements on which it operates, using double dispatch.

## The Problem It Solves

- Need to perform different operations on a heterogeneous object structure
- Want to add new operations without modifying existing element classes
- Operations are unrelated to the element classes' primary responsibilities
- Need to gather data or perform calculations across different element types

## The Solution

Create a visitor interface with visit methods for each element type. Elements accept visitors and call the appropriate visit method. This allows adding new operations by creating new visitor implementations without modifying elements.

## Quick Test

Ask yourself: "Do I need to perform various unrelated operations on a stable object structure?" If you want to add operations without modifying element classes, use Visitor.

## Examples

### Bad

```java
// Operations mixed with element classes - violates Single Responsibility
interface ComputerPart {
    void display();
    double calculatePrice(); // What if we need calculateWeight(), generateReport()?
    void performMaintenance(); // More operations make classes bloated
}

class CPU implements ComputerPart {
    private String model;
    private double basePrice;
    
    public CPU(String model, double basePrice) {
        this.model = model;
        this.basePrice = basePrice;
    }
    
    @Override
    public void display() {
        System.out.println("Displaying CPU: " + model);
    }
    
    @Override
    public double calculatePrice() {
        return basePrice * 1.2; // CPU markup
    }
    
    @Override
    public void performMaintenance() {
        System.out.println("Cleaning CPU thermal paste");
    }
    
    // Adding new operations requires modifying this class
    // What about calculatePowerConsumption()? generateSpecSheet()?
}

class Memory implements ComputerPart {
    private int sizeGB;
    private double basePrice;
    
    public Memory(int sizeGB, double basePrice) {
        this.sizeGB = sizeGB;
        this.basePrice = basePrice;
    }
    
    @Override
    public void display() {
        System.out.println("Displaying Memory: " + sizeGB + "GB");
    }
    
    @Override
    public double calculatePrice() {
        return basePrice * 1.1; // Memory markup
    }
    
    @Override
    public void performMaintenance() {
        System.out.println("Testing memory modules");
    }
    
    // Same problem - need to modify for new operations
}

class HardDrive implements ComputerPart {
    private int capacityGB;
    private double basePrice;
    
    public HardDrive(int capacityGB, double basePrice) {
        this.capacityGB = capacityGB;
        this.basePrice = basePrice;
    }
    
    @Override
    public void display() {
        System.out.println("Displaying Hard Drive: " + capacityGB + "GB");
    }
    
    @Override
    public double calculatePrice() {
        return basePrice * 1.15; // HDD markup
    }
    
    @Override
    public void performMaintenance() {
        System.out.println("Running disk defragmentation");
    }
    
    // Every new operation requires changing ALL element classes!
}
```

### Good

```java
// Element interface - focused only on accepting visitors
interface ComputerPart {
    void accept(ComputerPartVisitor visitor);
}

// Concrete elements - no business logic, just data and accept method
class CPU implements ComputerPart {
    private String model;
    private double basePrice;
    
    public CPU(String model, double basePrice) {
        this.model = model;
        this.basePrice = basePrice;
    }
    
    @Override
    public void accept(ComputerPartVisitor visitor) {
        visitor.visitCPU(this);
    }
    
    // Getters for visitor access
    public String getModel() { return model; }
    public double getBasePrice() { return basePrice; }
}

class Memory implements ComputerPart {
    private int sizeGB;
    private double basePrice;
    
    public Memory(int sizeGB, double basePrice) {
        this.sizeGB = sizeGB;
        this.basePrice = basePrice;
    }
    
    @Override
    public void accept(ComputerPartVisitor visitor) {
        visitor.visitMemory(this);
    }
    
    public int getSizeGB() { return sizeGB; }
    public double getBasePrice() { return basePrice; }
}

class HardDrive implements ComputerPart {
    private int capacityGB;
    private double basePrice;
    
    public HardDrive(int capacityGB, double basePrice) {
        this.capacityGB = capacityGB;
        this.basePrice = basePrice;
    }
    
    @Override
    public void accept(ComputerPartVisitor visitor) {
        visitor.visitHardDrive(this);
    }
    
    public int getCapacityGB() { return capacityGB; }
    public double getBasePrice() { return basePrice; }
}

class Computer implements ComputerPart {
    private ComputerPart[] parts;
    
    public Computer() {
        parts = new ComputerPart[] {
            new CPU("Intel i7", 300),
            new Memory(16, 150),
            new HardDrive(1000, 100)
        };
    }
    
    @Override
    public void accept(ComputerPartVisitor visitor) {
        for (ComputerPart part : parts) {
            part.accept(visitor);
        }
        visitor.visitComputer(this);
    }
    
    public ComputerPart[] getParts() { return parts; }
}

// Visitor interface - one method per element type
interface ComputerPartVisitor {
    void visitCPU(CPU cpu);
    void visitMemory(Memory memory);
    void visitHardDrive(HardDrive hardDrive);
    void visitComputer(Computer computer);
}

// Concrete visitors - each implements a specific operation
class DisplayVisitor implements ComputerPartVisitor {
    @Override
    public void visitCPU(CPU cpu) {
        System.out.println("Displaying CPU: " + cpu.getModel());
    }
    
    @Override
    public void visitMemory(Memory memory) {
        System.out.println("Displaying Memory: " + memory.getSizeGB() + "GB");
    }
    
    @Override
    public void visitHardDrive(HardDrive hardDrive) {
        System.out.println("Displaying Hard Drive: " + hardDrive.getCapacityGB() + "GB");
    }
    
    @Override
    public void visitComputer(Computer computer) {
        System.out.println("Displaying Computer with " + computer.getParts().length + " parts");
    }
}

class PriceCalculatorVisitor implements ComputerPartVisitor {
    private double totalPrice = 0;
    
    @Override
    public void visitCPU(CPU cpu) {
        double price = cpu.getBasePrice() * 1.2; // CPU markup
        totalPrice += price;
        System.out.println("CPU price: $" + price);
    }
    
    @Override
    public void visitMemory(Memory memory) {
        double price = memory.getBasePrice() * 1.1; // Memory markup
        totalPrice += price;
        System.out.println("Memory price: $" + price);
    }
    
    @Override
    public void visitHardDrive(HardDrive hardDrive) {
        double price = hardDrive.getBasePrice() * 1.15; // HDD markup
        totalPrice += price;
        System.out.println("Hard Drive price: $" + price);
    }
    
    @Override
    public void visitComputer(Computer computer) {
        System.out.println("Total Computer price: $" + totalPrice);
    }
    
    public double getTotalPrice() { return totalPrice; }
}

class MaintenanceVisitor implements ComputerPartVisitor {
    @Override
    public void visitCPU(CPU cpu) {
        System.out.println("CPU Maintenance: Cleaning thermal paste on " + cpu.getModel());
    }
    
    @Override
    public void visitMemory(Memory memory) {
        System.out.println("Memory Maintenance: Testing " + memory.getSizeGB() + "GB modules");
    }
    
    @Override
    public void visitHardDrive(HardDrive hardDrive) {
        System.out.println("HDD Maintenance: Defragmenting " + hardDrive.getCapacityGB() + "GB drive");
    }
    
    @Override
    public void visitComputer(Computer computer) {
        System.out.println("Computer Maintenance: System diagnostics completed");
    }
}

// New operations can be added easily without modifying elements
class PowerConsumptionVisitor implements ComputerPartVisitor {
    private double totalWatts = 0;
    
    @Override
    public void visitCPU(CPU cpu) {
        double watts = 95; // Typical CPU power consumption
        totalWatts += watts;
        System.out.println("CPU " + cpu.getModel() + " consumes: " + watts + "W");
    }
    
    @Override
    public void visitMemory(Memory memory) {
        double watts = memory.getSizeGB() * 0.5; // 0.5W per GB
        totalWatts += watts;
        System.out.println("Memory " + memory.getSizeGB() + "GB consumes: " + watts + "W");
    }
    
    @Override
    public void visitHardDrive(HardDrive hardDrive) {
        double watts = 6; // Typical HDD power consumption
        totalWatts += watts;
        System.out.println("Hard Drive " + hardDrive.getCapacityGB() + "GB consumes: " + watts + "W");
    }
    
    @Override
    public void visitComputer(Computer computer) {
        System.out.println("Total power consumption: " + totalWatts + "W");
    }
    
    public double getTotalWatts() { return totalWatts; }
}

// Usage - operations are performed by visitors
Computer computer = new Computer();

System.out.println("=== Display Information ===");
computer.accept(new DisplayVisitor());

System.out.println("\n=== Price Calculation ===");
PriceCalculatorVisitor priceVisitor = new PriceCalculatorVisitor();
computer.accept(priceVisitor);

System.out.println("\n=== Maintenance Tasks ===");
computer.accept(new MaintenanceVisitor());

System.out.println("\n=== Power Consumption Analysis ===");
PowerConsumptionVisitor powerVisitor = new PowerConsumptionVisitor();
computer.accept(powerVisitor);

// Easy to add new operations like WeightCalculatorVisitor, 
// WarrantyVisitor, etc. without modifying any element classes!
```