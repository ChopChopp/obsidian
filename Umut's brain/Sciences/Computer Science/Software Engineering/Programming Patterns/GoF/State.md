## Core Principal

Allow an object to alter its behavior when its internal state changes. The object appears to change its class by delegating behavior to state objects that represent different states.

## The Problem It Solves

- Object behavior changes based on its state
- Large conditional statements based on state variables
- State transitions are complex and hard to maintain
- Want to add new states without modifying existing code

## The Solution

Create separate state classes for each state, each implementing the same interface. The context object delegates behavior to the current state object and can change states by switching the state object reference.

## Quick Test

Ask yourself: "Does my object behave very differently based on its current state?" If you have complex state-dependent behavior with many conditionals, use State pattern.

## Examples

### Bad

```java
// State-dependent behavior handled with conditionals
class VendingMachine {
    private static final int NO_COIN = 0;
    private static final int HAS_COIN = 1;
    private static final int SOLD = 2;
    private static final int SOLD_OUT = 3;
    
    private int state = NO_COIN;
    private int itemCount = 10;
    
    public void insertCoin() {
        // Complex state-dependent logic with conditionals
        if (state == NO_COIN) {
            state = HAS_COIN;
            System.out.println("Coin inserted");
        } else if (state == HAS_COIN) {
            System.out.println("Coin already inserted");
        } else if (state == SOLD) {
            System.out.println("Please wait, dispensing item");
        } else if (state == SOLD_OUT) {
            System.out.println("Machine is sold out");
        }
    }
    
    public void ejectCoin() {
        if (state == NO_COIN) {
            System.out.println("No coin to eject");
        } else if (state == HAS_COIN) {
            state = NO_COIN;
            System.out.println("Coin ejected");
        } else if (state == SOLD) {
            System.out.println("Can't eject, already sold");
        } else if (state == SOLD_OUT) {
            System.out.println("No coin to eject");
        }
    }
    
    public void selectItem() {
        if (state == NO_COIN) {
            System.out.println("Insert coin first");
        } else if (state == HAS_COIN) {
            state = SOLD;
            dispense();
        } else if (state == SOLD) {
            System.out.println("Please wait, dispensing item");
        } else if (state == SOLD_OUT) {
            System.out.println("Machine is sold out");
        }
    }
    
    public void dispense() {
        if (state == NO_COIN) {
            System.out.println("No coin inserted");
        } else if (state == HAS_COIN) {
            System.out.println("Select item first");
        } else if (state == SOLD) {
            System.out.println("Item dispensed");
            itemCount--;
            if (itemCount == 0) {
                state = SOLD_OUT;
            } else {
                state = NO_COIN;
            }
        } else if (state == SOLD_OUT) {
            System.out.println("No items to dispense");
        }
    }
    // Adding new states requires modifying all methods!
}
```

### Good

```java
// State interface
interface VendingMachineState {
    void insertCoin();
    void ejectCoin();
    void selectItem();
    void dispense();
}

// Context class
class VendingMachine {
    private VendingMachineState noCoinState;
    private VendingMachineState hasCoinState;
    private VendingMachineState soldState;
    private VendingMachineState soldOutState;
    
    private VendingMachineState currentState;
    private int itemCount;
    
    public VendingMachine(int itemCount) {
        this.itemCount = itemCount;
        
        // Initialize all states
        noCoinState = new NoCoinState(this);
        hasCoinState = new HasCoinState(this);
        soldState = new SoldState(this);
        soldOutState = new SoldOutState(this);
        
        // Set initial state
        currentState = itemCount > 0 ? noCoinState : soldOutState;
    }
    
    // Delegate all behavior to current state
    public void insertCoin() { currentState.insertCoin(); }
    public void ejectCoin() { currentState.ejectCoin(); }
    public void selectItem() { currentState.selectItem(); }
    public void dispense() { currentState.dispense(); }
    
    // State transition methods
    public void setState(VendingMachineState state) { this.currentState = state; }
    
    // Getters for states
    public VendingMachineState getNoCoinState() { return noCoinState; }
    public VendingMachineState getHasCoinState() { return hasCoinState; }
    public VendingMachineState getSoldState() { return soldState; }
    public VendingMachineState getSoldOutState() { return soldOutState; }
    
    // Item management
    public void releaseItem() {
        if (itemCount > 0) {
            System.out.println("Item dispensed");
            itemCount--;
        }
    }
    
    public int getItemCount() { return itemCount; }
}

// Concrete state classes
class NoCoinState implements VendingMachineState {
    private VendingMachine machine;
    
    public NoCoinState(VendingMachine machine) {
        this.machine = machine;
    }
    
    @Override
    public void insertCoin() {
        System.out.println("Coin inserted");
        machine.setState(machine.getHasCoinState());
    }
    
    @Override
    public void ejectCoin() {
        System.out.println("No coin to eject");
    }
    
    @Override
    public void selectItem() {
        System.out.println("Insert coin first");
    }
    
    @Override
    public void dispense() {
        System.out.println("No coin inserted");
    }
}

class HasCoinState implements VendingMachineState {
    private VendingMachine machine;
    
    public HasCoinState(VendingMachine machine) {
        this.machine = machine;
    }
    
    @Override
    public void insertCoin() {
        System.out.println("Coin already inserted");
    }
    
    @Override
    public void ejectCoin() {
        System.out.println("Coin ejected");
        machine.setState(machine.getNoCoinState());
    }
    
    @Override
    public void selectItem() {
        System.out.println("Item selected");
        machine.setState(machine.getSoldState());
    }
    
    @Override
    public void dispense() {
        System.out.println("Select item first");
    }
}

class SoldState implements VendingMachineState {
    private VendingMachine machine;
    
    public SoldState(VendingMachine machine) {
        this.machine = machine;
    }
    
    @Override
    public void insertCoin() {
        System.out.println("Please wait, dispensing item");
    }
    
    @Override
    public void ejectCoin() {
        System.out.println("Can't eject, item already sold");
    }
    
    @Override
    public void selectItem() {
        System.out.println("Please wait, dispensing item");
    }
    
    @Override
    public void dispense() {
        machine.releaseItem();
        if (machine.getItemCount() == 0) {
            machine.setState(machine.getSoldOutState());
        } else {
            machine.setState(machine.getNoCoinState());
        }
    }
}

class SoldOutState implements VendingMachineState {
    private VendingMachine machine;
    
    public SoldOutState(VendingMachine machine) {
        this.machine = machine;
    }
    
    @Override
    public void insertCoin() {
        System.out.println("Machine is sold out");
    }
    
    @Override
    public void ejectCoin() {
        System.out.println("No coin to eject");
    }
    
    @Override
    public void selectItem() {
        System.out.println("Machine is sold out");
    }
    
    @Override
    public void dispense() {
        System.out.println("No items to dispense");
    }
}

// Usage - clean state transitions
VendingMachine machine = new VendingMachine(2);

machine.insertCoin();  // Coin inserted
machine.selectItem();  // Item selected
machine.dispense();    // Item dispensed

machine.insertCoin();  // Coin inserted
machine.selectItem();  // Item selected  
machine.dispense();    // Item dispensed (now sold out)

machine.insertCoin();  // Machine is sold out

// Adding new states (like MaintenanceState) only requires:
// 1. Creating the new state class
// 2. Adding transitions to/from existing states
// No modification of existing state classes needed!
```