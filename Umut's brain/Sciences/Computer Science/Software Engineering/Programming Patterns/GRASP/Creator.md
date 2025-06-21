# Core principal
*Assign creation responsibility to the class that has the closest relationship to the created object.*

# Who should create what?
Class A should create class B if A:
1. Contains or aggregates B objects
2. Records instances of B objects
3. Closely uses B objects
4. Has the data needed to initialize B

# The problem it solves
- Random classes creating objects they have no business creating
- Creation logic scattered everywhere
- High coupling between unrelated classes
- Unclear responsibilities for object instantiation

# The solution
Only instantiate objects from the closest relationship.

# Benefits
- **Clear responsibilities** - obvious who creates what
- **Better cohesion** - creation happens near related data
- **Lower coupling** - fewer classes involved in creation
- **Easier testing** - test creation logic with the right class

# Quick test
Ask: "*What class contains this object, or has the closest relationship to it?*" That's your creator.

# Examples
> Bad
```java
// ShoppingService creates CartItem but doesn't contain CartItems
class ShoppingService {
	public void addProductToCart(String cartId, String productId, int quantity) {
		ShoppingCart cart = getCart(cartId);
		Product product = getProduct(productId);

		// WRONG: Service creates CartItem
		CartItem item = new CartItem(product, quantity);
		card.addItem(item);
	}
}
```

> Good
```java
// ShoppingCart creates CartItem because it contains CartItems
class ShoppingCart {
	private List<CartItem> items;

	public void addProduct(Product product, int quantity) {
		// RIGHT: Cart creates CartItem (Cart contains CartItems)
		CartItem newItem = new CartItem(product, quantity);
		items.add(newItem);
	}

	public Order createOrder() {
		// RIGHT: Cart creates Order (Cart has the data needed to create an Order)
		return new Order(generateId(), items, LocalDate.now(), getTotalAmount());
	}
}
```