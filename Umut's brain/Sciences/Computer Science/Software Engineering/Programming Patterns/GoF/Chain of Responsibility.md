## Core Principal

Pass requests along a chain of handlers until one handles the request. Each handler decides either to process the request or pass it to the next handler in the chain, decoupling senders from receivers.

## The Problem It Solves

- Multiple objects can handle a request, but the handler isn't known in advance
- Want to issue requests without specifying the receiver explicitly
- Need to dynamically configure which objects handle requests
- Want to avoid coupling request sender to specific handlers

## The Solution

Create a chain of handler objects, each with a reference to the next handler. When a request comes in, each handler either processes it or passes it to the next handler in the chain.

## Quick Test

Ask yourself: "Do I have multiple objects that could handle a request, and I want to give them a chance to handle it in sequence?" If the handler varies at runtime and you want loose coupling, use Chain of Responsibility.

## Examples

### Bad

```java
// Tightly coupled request handling
class SupportTicket {
    private String issue;
    private String priority;
    
    public SupportTicket(String issue, String priority) {
        this.issue = issue;
        this.priority = priority;
    }
    
    // Getters omitted for brevity
    public String getIssue() { return issue; }
    public String getPriority() { return priority; }
}

class TicketProcessor {
    public void processTicket(SupportTicket ticket) {
        // Tightly coupled - all logic in one place
        if (ticket.getPriority().equals("LOW")) {
            System.out.println("Junior support handles: " + ticket.getIssue());
        } else if (ticket.getPriority().equals("MEDIUM")) {
            System.out.println("Senior support handles: " + ticket.getIssue());
        } else if (ticket.getPriority().equals("HIGH")) {
            System.out.println("Manager handles: " + ticket.getIssue());
        } else {
            System.out.println("Escalated to director: " + ticket.getIssue());
        }
        // Adding new handlers requires modifying this method
    }
}
```

### Good

```java
// Request object
class SupportTicket {
    private String issue;
    private String priority;
    
    public SupportTicket(String issue, String priority) {
        this.issue = issue;
        this.priority = priority;
    }
    
    public String getIssue() { return issue; }
    public String getPriority() { return priority; }
}

// Abstract handler
abstract class SupportHandler {
    protected SupportHandler nextHandler;
    
    public void setNext(SupportHandler handler) {
        this.nextHandler = handler;
    }
    
    public abstract void handleTicket(SupportTicket ticket);
}

// Concrete handlers
class JuniorSupport extends SupportHandler {
    @Override
    public void handleTicket(SupportTicket ticket) {
        if (ticket.getPriority().equals("LOW")) {
            System.out.println("Junior support handles: " + ticket.getIssue());
        } else if (nextHandler != null) {
            nextHandler.handleTicket(ticket);
        }
    }
}

class SeniorSupport extends SupportHandler {
    @Override
    public void handleTicket(SupportTicket ticket) {
        if (ticket.getPriority().equals("MEDIUM")) {
            System.out.println("Senior support handles: " + ticket.getIssue());
        } else if (nextHandler != null) {
            nextHandler.handleTicket(ticket);
        }
    }
}

class Manager extends SupportHandler {
    @Override
    public void handleTicket(SupportTicket ticket) {
        if (ticket.getPriority().equals("HIGH")) {
            System.out.println("Manager handles: " + ticket.getIssue());
        } else if (nextHandler != null) {
            nextHandler.handleTicket(ticket);
        }
    }
}

class Director extends SupportHandler {
    @Override
    public void handleTicket(SupportTicket ticket) {
        System.out.println("Director handles critical issue: " + ticket.getIssue());
    }
}

// Usage - flexible chain configuration
JuniorSupport junior = new JuniorSupport();
SeniorSupport senior = new SeniorSupport();
Manager manager = new Manager();
Director director = new Director();

junior.setNext(senior);
senior.setNext(manager);
manager.setNext(director);

// Process tickets
SupportTicket lowTicket = new SupportTicket("Password reset", "LOW");
SupportTicket highTicket = new SupportTicket("System down", "HIGH");

junior.handleTicket(lowTicket);  // Handled by junior
junior.handleTicket(highTicket); // Passed through chain to manager
```