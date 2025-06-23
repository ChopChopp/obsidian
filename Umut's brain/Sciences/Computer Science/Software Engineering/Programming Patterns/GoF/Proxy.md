## Core Principal

Provide a placeholder or surrogate for another object to control access to it. The proxy implements the same interface as the real object and can add functionality like caching, access control, or lazy loading.

## The Problem It Solves

- Need to control access to an object (security, permissions)
- Want to add functionality without modifying the original object
- Need lazy loading or caching for expensive operations
- Want to add logging, monitoring, or other cross-cutting concerns

## The Solution

Create a proxy class that implements the same interface as the real object. The proxy maintains a reference to the real object and delegates requests to it, potentially adding additional behavior before or after the delegation.

## Quick Test

Ask yourself: "Do I need to control or enhance access to an object without changing its interface?" If you need to add access control, caching, logging, or lazy loading, use Proxy.

## Examples

### Bad

```java
// Direct access to expensive operations without control
interface Image {
    void display();
}

class RealImage implements Image {
    private String filename;
    
    public RealImage(String filename) {
        this.filename = filename;
        loadFromDisk(); // Expensive operation happens immediately
    }
    
    private void loadFromDisk() {
        System.out.println("Loading " + filename + " from disk...");
        // Simulate expensive loading operation
        try {
            Thread.sleep(2000);
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
    }
    
    @Override
    public void display() {
        System.out.println("Displaying " + filename);
    }
}

// Client code - no control over when expensive operations happen
class ImageViewer {
    public void showImages() {
        // All images are loaded immediately, even if not displayed
        Image image1 = new RealImage("photo1.jpg"); // Expensive!
        Image image2 = new RealImage("photo2.jpg"); // Expensive!
        Image image3 = new RealImage("photo3.jpg"); // Expensive!
        
        // Only display one image
        image1.display();
        // image2 and image3 were loaded unnecessarily
    }
}
```

### Good

```java
// Same interface for real object and proxy
interface Image {
    void display();
}

// Real object with expensive operations
class RealImage implements Image {
    private String filename;
    
    public RealImage(String filename) {
        this.filename = filename;
        loadFromDisk();
    }
    
    private void loadFromDisk() {
        System.out.println("Loading " + filename + " from disk...");
        // Simulate expensive loading operation
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
    }
    
    @Override
    public void display() {
        System.out.println("Displaying " + filename);
    }
}

// Proxy that controls access and adds functionality
class ImageProxy implements Image {
    private RealImage realImage;
    private String filename;
    private boolean hasAccess;
    private boolean isLoaded = false;
    
    public ImageProxy(String filename, boolean hasAccess) {
        this.filename = filename;
        this.hasAccess = hasAccess;
    }
    
    @Override
    public void display() {
        // Access control
        if (!hasAccess) {
            System.out.println("Access denied: Cannot display " + filename);
            return;
        }
        
        // Lazy loading - only create real object when needed
        if (!isLoaded) {
            System.out.println("Proxy: Loading image for first time...");
            realImage = new RealImage(filename);
            isLoaded = true;
        }
        
        // Additional functionality - logging
        System.out.println("Proxy: Logging image access for " + filename);
        
        // Delegate to real object
        realImage.display();
    }
    
    // Additional proxy functionality
    public void preload() {
        if (!isLoaded && hasAccess) {
            System.out.println("Proxy: Preloading " + filename);
            realImage = new RealImage(filename);
            isLoaded = true;
        }
    }
}

// Another type of proxy - Caching Proxy
class CachingImageProxy implements Image {
    private RealImage realImage;
    private String filename;
    private static Map<String, RealImage> cache = new HashMap<>();
    
    public CachingImageProxy(String filename) {
        this.filename = filename;
    }
    
    @Override
    public void display() {
        // Check cache first
        if (cache.containsKey(filename)) {
            System.out.println("Proxy: Using cached image");
            realImage = cache.get(filename);
        } else {
            System.out.println("Proxy: Loading and caching image");
            realImage = new RealImage(filename);
            cache.put(filename, realImage);
        }
        
        realImage.display();
    }
}

// Usage - controlled access and lazy loading
class ImageViewer {
    public void showImages() {
        // Images are not loaded until actually displayed
        Image image1 = new ImageProxy("photo1.jpg", true);
        Image image2 = new ImageProxy("photo2.jpg", false); // No access
        Image image3 = new ImageProxy("photo3.jpg", true);
        
        System.out.println("=== Displaying images ===");
        image1.display(); // Loads and displays
        image2.display(); // Access denied
        image1.display(); // Uses already loaded image
        
        // Caching proxy
        Image cachedImage1 = new CachingImageProxy("cached1.jpg");
        Image cachedImage2 = new CachingImageProxy("cached1.jpg"); // Same file
        
        cachedImage1.display(); // Loads and caches
        cachedImage2.display(); // Uses cache
    }
}
```