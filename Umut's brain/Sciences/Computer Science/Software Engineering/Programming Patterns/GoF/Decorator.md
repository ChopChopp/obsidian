## Core Principal

Add new functionality to objects dynamically without altering their structure. Wrap objects with decorator classes that extend behavior while maintaining the original interface.

## The Problem It Solves

- Need to add responsibilities to objects dynamically
- Want to extend functionality without subclassing
- Need to combine different behaviors flexibly
- Subclassing would result in too many classes (combinatorial explosion)

## The Solution

Create decorator classes that wrap the original object and implement the same interface. Decorators can add behavior before/after delegating to the wrapped object, and can be stacked for multiple enhancements.

## Quick Test

Ask yourself: "Do I need to add behavior to objects at runtime in different combinations?" If inheritance would create too many subclasses or you need flexible composition, use Decorator.

## Examples

### Bad

```java
// Explosion of subclasses for different combinations
interface Coffee {
    double cost();
    String description();
}

class SimpleCoffee implements Coffee {
    public double cost() { return 2.0; }
    public String description() { return "Simple coffee"; }
}

// Need separate class for each combination!
class CoffeeWithMilk implements Coffee {
    public double cost() { return 2.5; }
    public String description() { return "Coffee with milk"; }
}

class CoffeeWithSugar implements Coffee {
    public double cost() { return 2.2; }
    public String description() { return "Coffee with sugar"; }
}

class CoffeeWithMilkAndSugar implements Coffee {
    public double cost() { return 2.7; }
    public String description() { return "Coffee with milk and sugar"; }
}

class CoffeeWithMilkSugarAndWhip implements Coffee {
    public double cost() { return 3.2; }
    public String description() { return "Coffee with milk, sugar and whip"; }
}

// What about CoffeeWithWhip? CoffeeWithSugarAndWhip? 
// This leads to exponential class explosion!
```

### Good

```java
// Base component interface
interface Coffee {
    double cost();
    String description();
}

// Concrete component
class SimpleCoffee implements Coffee {
    @Override
    public double cost() {
        return 2.0;
    }
    
    @Override
    public String description() {
        return "Simple coffee";
    }
}

// Base decorator
abstract class CoffeeDecorator implements Coffee {
    protected Coffee coffee;
    
    public CoffeeDecorator(Coffee coffee) {
        this.coffee = coffee;
    }
    
    @Override
    public double cost() {
        return coffee.cost();
    }
    
    @Override
    public String description() {
        return coffee.description();
    }
}

// Concrete decorators
class MilkDecorator extends CoffeeDecorator {
    public MilkDecorator(Coffee coffee) {
        super(coffee);
    }
    
    @Override
    public double cost() {
        return super.cost() + 0.5;
    }
    
    @Override
    public String description() {
        return super.description() + ", milk";
    }
}

class SugarDecorator extends CoffeeDecorator {
    public SugarDecorator(Coffee coffee) {
        super(coffee);
    }
    
    @Override
    public double cost() {
        return super.cost() + 0.2;
    }
    
    @Override
    public String description() {
        return super.description() + ", sugar";
    }
}

class WhipDecorator extends CoffeeDecorator {
    public WhipDecorator(Coffee coffee) {
        super(coffee);
    }
    
    @Override
    public double cost() {
        return super.cost() + 0.7;
    }
    
    @Override
    public String description() {
        return super.description() + ", whip";
    }
}

// Usage - flexible composition at runtime
Coffee coffee = new SimpleCoffee();
System.out.println(coffee.description() + " costs $" + coffee.cost());

// Add milk
coffee = new MilkDecorator(coffee);
System.out.println(coffee.description() + " costs $" + coffee.cost());

// Add sugar
coffee = new SugarDecorator(coffee);
System.out.println(coffee.description() + " costs $" + coffee.cost());

// Add whip
coffee = new WhipDecorator(coffee);
System.out.println(coffee.description() + " costs $" + coffee.cost());
// Output: Simple coffee, milk, sugar, whip costs $3.4
```