## Core Principal

Define an interface for creating objects, but let subclasses decide which class to instantiate. Factory Method lets a class defer instantiation to subclasses, promoting loose coupling between creator and product classes.

## The Problem It Solves

- Need to create objects without specifying their exact classes
- Want to delegate object creation to subclasses
- Need to eliminate tight coupling between creator and concrete products
- Want to follow the Open/Closed Principle for adding new product types

## The Solution

Create an abstract creator class with a factory method that returns a product interface. Concrete creators override the factory method to return specific product implementations, allowing customization of object creation.

## Quick Test

Ask yourself: "Do I need to create objects but want subclasses to decide the exact type?" If you want to delegate object creation decisions to subclasses, use Factory Method.

## Examples

### Bad

```java
// Tightly coupled object creation
class LogisticApp {
    private String transportType;
    
    public LogisticApp(String transportType) {
        this.transportType = transportType;
    }
    
    public void planDelivery() {
        Transport transport;
        
        // Tight coupling - violates Open/Closed Principle
        if (transportType.equals("truck")) {
            transport = new Truck();
        } else if (transportType.equals("ship")) {
            transport = new Ship();
        } else {
            throw new IllegalArgumentException("Unknown transport type");
        }
        
        transport.deliver();
        // Adding new transport types requires modifying this code!
    }
}

interface Transport {
    void deliver();
}

class Truck implements Transport {
    public void deliver() {
        System.out.println("Delivering by truck on road");
    }
}

class Ship implements Transport {
    public void deliver() {
        System.out.println("Delivering by ship on sea");
    }
}
```

### Good

```java
// Product interface
interface Transport {
    void deliver();
}

// Concrete products
class Truck implements Transport {
    @Override
    public void deliver() {
        System.out.println("Delivering by truck on road");
    }
}

class Ship implements Transport {
    @Override
    public void deliver() {
        System.out.println("Delivering by ship on sea");
    }
}

class Airplane implements Transport {
    @Override
    public void deliver() {
        System.out.println("Delivering by airplane in the sky");
    }
}

// Abstract creator
abstract class Logistics {
    // Factory method - subclasses will override this
    public abstract Transport createTransport();
    
    // Template method that uses the factory method
    public void planDelivery() {
        Transport transport = createTransport();
        transport.deliver();
    }
}

// Concrete creators
class RoadLogistics extends Logistics {
    @Override
    public Transport createTransport() {
        return new Truck();
    }
}

class SeaLogistics extends Logistics {
    @Override
    public Transport createTransport() {
        return new Ship();
    }
}

class AirLogistics extends Logistics {
    @Override
    public Transport createTransport() {
        return new Airplane();
    }
}

// Usage - easy to extend without modifying existing code
Logistics roadLogistics = new RoadLogistics();
roadLogistics.planDelivery(); // Uses truck

Logistics seaLogistics = new SeaLogistics();
seaLogistics.planDelivery(); // Uses ship

Logistics airLogistics = new AirLogistics();
airLogistics.planDelivery(); // Uses airplane

// Adding new transport types only requires new concrete creators
```