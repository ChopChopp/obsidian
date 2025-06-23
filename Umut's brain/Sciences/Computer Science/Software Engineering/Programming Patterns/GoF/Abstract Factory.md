## Core Principal

Provide an interface for creating families of related or dependent objects without specifying their concrete classes. The pattern encapsulates object creation logic and ensures that created objects are compatible with each other.

## The Problem It Solves

- Need to create families of related objects that must be used together
- Want to ensure compatibility between related objects
- Need to support multiple product families without changing client code
- Want to isolate object creation logic from business logic

## The Solution

Create an abstract factory interface that declares methods for creating each type of product. Implement concrete factories for each product family. Clients use the abstract factory interface, remaining independent of concrete implementations.

## Quick Test

Ask yourself: "Do I need to create families of related objects that must work together?" If yes, and you want to ensure compatibility while keeping creation logic separate, use Abstract Factory.

## Examples

### Bad

```java
// Tightly coupled to specific implementations
public class UIRenderer {
    public void render() {
        // Hard-coded to specific implementations
        WindowsButton button = new WindowsButton();
        WindowsCheckbox checkbox = new WindowsCheckbox();
        
        button.render();
        checkbox.render();
        
        // What if we need Mac UI? We'd have to change this code!
    }
}

class WindowsButton {
    public void render() { System.out.println("Rendering Windows button"); }
}

class WindowsCheckbox {
    public void render() { System.out.println("Rendering Windows checkbox"); }
}
```

### Good

```java
// Abstract factory interface
interface UIFactory {
    Button createButton();
    Checkbox createCheckbox();
}

// Abstract products
interface Button {
    void render();
}

interface Checkbox {
    void render();
}

// Concrete Windows family
class WindowsUIFactory implements UIFactory {
    public Button createButton() { return new WindowsButton(); }
    public Checkbox createCheckbox() { return new WindowsCheckbox(); }
}

class WindowsButton implements Button {
    public void render() { System.out.println("Rendering Windows button"); }
}

class WindowsCheckbox implements Checkbox {
    public void render() { System.out.println("Rendering Windows checkbox"); }
}

// Concrete Mac family
class MacUIFactory implements UIFactory {
    public Button createButton() { return new MacButton(); }
    public Checkbox createCheckbox() { return new MacCheckbox(); }
}

class MacButton implements Button {
    public void render() { System.out.println("Rendering Mac button"); }
}

class MacCheckbox implements Checkbox {
    public void render() { System.out.println("Rendering Mac checkbox"); }
}

// Client code - works with any UI family
class UIRenderer {
    private UIFactory factory;
    
    public UIRenderer(UIFactory factory) {
        this.factory = factory;
    }
    
    public void render() {
        Button button = factory.createButton();
        Checkbox checkbox = factory.createCheckbox();
        
        button.render();
        checkbox.render();
    }
}

// Usage
UIRenderer windowsRenderer = new UIRenderer(new WindowsUIFactory());
UIRenderer macRenderer = new UIRenderer(new MacUIFactory());
```