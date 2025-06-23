## Core Principal

Provide a simplified interface to a complex subsystem. Hide the complexity of multiple classes and their interactions behind a single, easy-to-use interface.

## The Problem It Solves

- Complex subsystems are difficult to use and understand
- Clients need to interact with many classes to accomplish simple tasks
- Want to decouple clients from subsystem implementation details
- Need to provide different levels of access to subsystem functionality

## The Solution

Create a facade class that provides simple methods for common tasks. The facade delegates to the appropriate subsystem classes, hiding their complexity from clients while still allowing direct access when needed.

## Quick Test

Ask yourself: "Do clients have to interact with many complex classes to perform common operations?" If you want to simplify the interface to a complex system, use Facade.

## Examples

### Bad

```java
// Complex subsystem with many interdependent classes
class CPU {
    public void freeze() { System.out.println("CPU frozen"); }
    public void jump(long position) { System.out.println("CPU jumping to " + position); }
    public void execute() { System.out.println("CPU executing"); }
}

class Memory {
    public void load(long position, byte[] data) {
        System.out.println("Loading data to memory position " + position);
    }
}

class HardDrive {
    public byte[] read(long lba, int size) {
        System.out.println("Reading " + size + " bytes from sector " + lba);
        return new byte[size];
    }
}

// Client must understand and coordinate all subsystem components
class Client {
    public void startComputer() {
        // Complex sequence that client must remember and manage
        CPU cpu = new CPU();
        Memory memory = new Memory();
        HardDrive hardDrive = new HardDrive();
        
        cpu.freeze();
        memory.load(0, hardDrive.read(0, 1024));
        cpu.jump(0);
        cpu.execute();
        
        // If the startup sequence changes, all clients must be updated!
    }
}
```

### Good

```java
// Complex subsystem classes (same as above)
class CPU {
    public void freeze() { System.out.println("CPU frozen"); }
    public void jump(long position) { System.out.println("CPU jumping to " + position); }
    public void execute() { System.out.println("CPU executing"); }
}

class Memory {
    public void load(long position, byte[] data) {
        System.out.println("Loading data to memory position " + position);
    }
}

class HardDrive {
    public byte[] read(long lba, int size) {
        System.out.println("Reading " + size + " bytes from sector " + lba);
        return new byte[size];
    }
}

// Facade provides simplified interface
class ComputerFacade {
    private CPU cpu;
    private Memory memory;
    private HardDrive hardDrive;
    
    public ComputerFacade() {
        this.cpu = new CPU();
        this.memory = new Memory();
        this.hardDrive = new HardDrive();
    }
    
    // Simple method that hides complex subsystem interactions
    public void start() {
        System.out.println("Starting computer...");
        cpu.freeze();
        memory.load(0, hardDrive.read(0, 1024));
        cpu.jump(0);
        cpu.execute();
        System.out.println("Computer started successfully!");
    }
    
    // Another simplified operation
    public void shutdown() {
        System.out.println("Shutting down computer...");
        // Complex shutdown sequence hidden from client
        cpu.freeze();
        // Additional shutdown steps...
        System.out.println("Computer shut down safely!");
    }
    
    // Still allow direct access if needed
    public CPU getCpu() { return cpu; }
    public Memory getMemory() { return memory; }
    public HardDrive getHardDrive() { return hardDrive; }
}

// Client code is much simpler
class Client {
    public void useComputer() {
        ComputerFacade computer = new ComputerFacade();
        
        // Simple, intuitive interface
        computer.start();
        
        // Do some work...
        
        computer.shutdown();
        
        // Can still access subsystem directly if needed
        if (/* some special condition */) {
            computer.getCpu().execute();
        }
    }
}

// Usage
Client client = new Client();
client.useComputer();
```