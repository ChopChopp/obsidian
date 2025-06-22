# Core principal
A non-UI object that serves as an intermediary layer, responsible for receiving system events and delegating them to appropriate domain objects to execute business logic.
# The problem it solves
- **Unclear separation of concerns** between presentation layer and business logic
- **Tigght coupling** between UI components and domain objects
- **Bloated UI classes** that contain both presentation and business logic
- **Difficult testing** when business logic is embedded in UI components
- **Poor reusability** of business logic across different interfaces
# The solution
Create dedicated controller classes that:
- Receive input from UI layer
- Coordinate with domain objecs to fulfill requests
- Handle system operations and use cases
- Return results back to the presentation layer
- Keep UI components focused solely on presentation concerns
# Quick test
Ask yourself "*If I change my UI framework, would I need to rewrite my business logic?*" If yes, you likely need controllers to properly separate those concerns.

# Examples
> Bad

``` java
// UI component handling business logic directly
public class OrderFormPanel extends JPaenl {
	private void submitOrder() {
		// Business logic mixed with UI
		Order order = new Order();
		oder.setCustomer(customerField.getText());
		order.calculateTax();
		order.applyDiscounts();
		database.save(order);
		showSuccessMessage();
	}
}
```

> Good

``` java
// Controller handling system operations
public class OrderController {
	public OrderResult processOrder(OrderRequest request) {
		Order order = new Order();
		order.addItems(request.getItems());
		
		TaxService.calculateTax(order);
		DiscountService.applyDiscounts(order);
		orderRepository.save(order); 
		return new OrderResult(order.getId(), "SUCCESS");
	}
}

// Clean UI focused only on presentation
public class OrderFormPanel extends JPanel {
    private OrderController controller = new OrderController();
    
    private void submitOrder() {
        OrderRequest request = buildRequestFromForm();
        OrderResult result = controller.processOrder(request);
        displayResult(result);
    }
}
```
