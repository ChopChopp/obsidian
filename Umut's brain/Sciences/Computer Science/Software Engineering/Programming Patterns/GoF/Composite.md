## Core Principal

Compose objects into tree structures to represent part-whole hierarchies. Allows clients to treat individual objects and compositions of objects uniformly through a common interface.

## The Problem It Solves

- Need to represent hierarchical structures (trees) of objects
- Want to treat individual objects and compositions uniformly
- Need to perform operations on both single items and groups
- Want to build complex structures from simple components

## The Solution

Create a common interface for both leaf objects and composite objects. Composite objects contain collections of components (which can be leaves or other composites) and delegate operations to their children.

## Quick Test

Ask yourself: "Do I have a tree-like structure where I want to treat individual items and groups of items the same way?" If you need uniform treatment of parts and wholes, use Composite.

## Examples

### Bad

```java
// Different classes for files and folders - no uniform treatment
class File {
    private String name;
    private int size;
    
    public File(String name, int size) {
        this.name = name;
        this.size = size;
    }
    
    public void display() {
        System.out.println("File: " + name + " (" + size + " KB)");
    }
    
    public int getSize() { return size; }
}

class Folder {
    private String name;
    private List<File> files;
    private List<Folder> subFolders;
    
    public Folder(String name) {
        this.name = name;
        this.files = new ArrayList<>();
        this.subFolders = new ArrayList<>();
    }
    
    // Client must handle files and folders differently
    public void display() {
        System.out.println("Folder: " + name);
        for (File file : files) {
            file.display(); // Different method call
        }
        for (Folder folder : subFolders) {
            folder.display(); // Same method but different logic needed
        }
    }
    
    // Complex size calculation
    public int getSize() {
        int total = 0;
        for (File file : files) {
            total += file.getSize();
        }
        for (Folder folder : subFolders) {
            total += folder.getSize();
        }
        return total;
    }
}
```

### Good

```java
// Common interface for all file system components
interface FileSystemComponent {
    void display();
    int getSize();
    void add(FileSystemComponent component); // Optional for leaves
    void remove(FileSystemComponent component); // Optional for leaves
}

// Leaf component - individual file
class File implements FileSystemComponent {
    private String name;
    private int size;
    
    public File(String name, int size) {
        this.name = name;
        this.size = size;
    }
    
    @Override
    public void display() {
        System.out.println("File: " + name + " (" + size + " KB)");
    }
    
    @Override
    public int getSize() {
        return size;
    }
    
    @Override
    public void add(FileSystemComponent component) {
        throw new UnsupportedOperationException("Cannot add to a file");
    }
    
    @Override
    public void remove(FileSystemComponent component) {
        throw new UnsupportedOperationException("Cannot remove from a file");
    }
}

// Composite component - folder containing other components
class Folder implements FileSystemComponent {
    private String name;
    private List<FileSystemComponent> components;
    
    public Folder(String name) {
        this.name = name;
        this.components = new ArrayList<>();
    }
    
    @Override
    public void display() {
        System.out.println("Folder: " + name);
        for (FileSystemComponent component : components) {
            component.display(); // Uniform treatment!
        }
    }
    
    @Override
    public int getSize() {
        int total = 0;
        for (FileSystemComponent component : components) {
            total += component.getSize(); // Uniform treatment!
        }
        return total;
    }
    
    @Override
    public void add(FileSystemComponent component) {
        components.add(component);
    }
    
    @Override
    public void remove(FileSystemComponent component) {
        components.remove(component);
    }
}

// Usage - treat files and folders uniformly
Folder root = new Folder("root");
Folder documents = new Folder("documents");
File resume = new File("resume.pdf", 150);
File photo = new File("photo.jpg", 2000);

documents.add(resume);
root.add(documents);
root.add(photo);

// Uniform operations on the entire tree
root.display();
System.out.println("Total size: " + root.getSize() + " KB");
```