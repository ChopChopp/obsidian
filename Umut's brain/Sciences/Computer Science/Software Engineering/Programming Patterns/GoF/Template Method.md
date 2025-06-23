## Core Principal

Define the skeleton of an algorithm in a base class, letting subclasses override specific steps without changing the algorithm's structure. Promotes code reuse while allowing customization of specific parts.

## The Problem It Solves

- Multiple classes share similar algorithms with slight variations
- Want to avoid code duplication while allowing customization
- Need to control the algorithm structure but allow step customization
- Want to implement the "Hollywood Principle" (don't call us, we'll call you)

## The Solution

Create an abstract base class with a template method that defines the algorithm steps. Mark some steps as abstract (must be implemented by subclasses) and others as hooks (optional to override).

## Quick Test

Ask yourself: "Do I have similar algorithms that differ only in a few steps?" If you want to reuse the common structure while customizing specific parts, use Template Method.

## Examples

### Bad

```java
// Duplicated algorithm structure across multiple classes
class PdfReport {
    public void generateReport(String data) {
        // Step 1: Setup - duplicated in all report types
        System.out.println("Setting up PDF report generation");
        System.out.println("Initializing PDF library");
        
        // Step 2: Header - duplicated logic
        System.out.println("Adding PDF header");
        System.out.println("Adding company logo");
        
        // Step 3: Content - PDF specific
        System.out.println("Converting data to PDF format");
        System.out.println("Adding PDF styling: " + data);
        
        // Step 4: Footer - duplicated logic
        System.out.println("Adding PDF footer");
        System.out.println("Adding page numbers");
        
        // Step 5: Finalize - duplicated in all report types
        System.out.println("Finalizing PDF report");
        System.out.println("Saving to file system");
    }
}

class ExcelReport {
    public void generateReport(String data) {
        // Step 1: Setup - DUPLICATED!
        System.out.println("Setting up Excel report generation");
        System.out.println("Initializing Excel library");
        
        // Step 2: Header - DUPLICATED!
        System.out.println("Adding Excel header");
        System.out.println("Adding company logo");
        
        // Step 3: Content - Excel specific
        System.out.println("Converting data to Excel format");
        System.out.println("Creating Excel cells: " + data);
        System.out.println("Applying Excel formulas");
        
        // Step 4: Footer - DUPLICATED!
        System.out.println("Adding Excel footer");
        System.out.println("Adding sheet numbers");
        
        // Step 5: Finalize - DUPLICATED!
        System.out.println("Finalizing Excel report");
        System.out.println("Saving to file system");
    }
}

class HtmlReport {
    public void generateReport(String data) {
        // Step 1: Setup - DUPLICATED AGAIN!
        System.out.println("Setting up HTML report generation");
        System.out.println("Initializing HTML renderer");
        
        // Step 2: Header - DUPLICATED AGAIN!
        System.out.println("Adding HTML header");
        System.out.println("Adding company logo");
        
        // Step 3: Content - HTML specific
        System.out.println("Converting data to HTML format");
        System.out.println("Adding HTML tags: " + data);
        System.out.println("Applying CSS styles");
        
        // Step 4: Footer - DUPLICATED AGAIN!
        System.out.println("Adding HTML footer");
        
        // Step 5: Finalize - DUPLICATED AGAIN!
        System.out.println("Finalizing HTML report");
        System.out.println("Saving to file system");
    }
}
```

### Good

```java
// Abstract base class with template method
abstract class ReportGenerator {
    
    // Template method - defines the algorithm skeleton
    public final void generateReport(String data) {
        // Fixed algorithm structure that cannot be changed by subclasses
        setup();
        addHeader();
        addContent(data);
        addFooter();
        finalize();
    }
    
    // Common steps implemented in base class
    private void setup() {
        System.out.println("Setting up " + getReportType() + " report generation");
        initializeLibrary();
    }
    
    private void addHeader() {
        System.out.println("Adding " + getReportType() + " header");
        System.out.println("Adding company logo");
        // Hook method - optional customization
        customizeHeader();
    }
    
    private void addFooter() {
        System.out.println("Adding " + getReportType() + " footer");
        // Hook method - optional customization
        customizeFooter();
    }
    
    private void finalize() {
        System.out.println("Finalizing " + getReportType() + " report");
        System.out.println("Saving to file system");
        // Hook method - optional cleanup
        cleanup();
    }
    
    // Abstract methods - must be implemented by subclasses
    protected abstract void initializeLibrary();
    protected abstract void addContent(String data);
    protected abstract String getReportType();
    
    // Hook methods - optional to override
    protected void customizeHeader() {
        // Default: do nothing
    }
    
    protected void customizeFooter() {
        // Default: do nothing
    }
    
    protected void cleanup() {
        // Default: do nothing
    }
}

// Concrete implementations
class PdfReport extends ReportGenerator {
    @Override
    protected void initializeLibrary() {
        System.out.println("Initializing PDF library");
    }
    
    @Override
    protected void addContent(String data) {
        System.out.println("Converting data to PDF format");
        System.out.println("Adding PDF styling: " + data);
    }
    
    @Override
    protected String getReportType() {
        return "PDF";
    }
    
    @Override
    protected void customizeFooter() {
        System.out.println("Adding page numbers");
    }
}

class ExcelReport extends ReportGenerator {
    @Override
    protected void initializeLibrary() {
        System.out.println("Initializing Excel library");
    }
    
    @Override
    protected void addContent(String data) {
        System.out.println("Converting data to Excel format");
        System.out.println("Creating Excel cells: " + data);
        System.out.println("Applying Excel formulas");
    }
    
    @Override
    protected String getReportType() {
        return "Excel";
    }
    
    @Override
    protected void customizeFooter() {
        System.out.println("Adding sheet numbers");
    }
}

class HtmlReport extends ReportGenerator {
    @Override
    protected void initializeLibrary() {
        System.out.println("Initializing HTML renderer");
    }
    
    @Override
    protected void addContent(String data) {
        System.out.println("Converting data to HTML format");
        System.out.println("Adding HTML tags: " + data);
        System.out.println("Applying CSS styles");
    }
    
    @Override
    protected String getReportType() {
        return "HTML";
    }
    
    // Uses default hook methods (no customization needed)
}

// Usage - consistent algorithm structure with customized steps
ReportGenerator pdfReport = new PdfReport();
pdfReport.generateReport("Sales data for Q4");

System.out.println();

ReportGenerator excelReport = new ExcelReport();
excelReport.generateReport("Financial data for Q4");

System.out.println();

ReportGenerator htmlReport = new HtmlReport();
htmlReport.generateReport("Marketing data for Q4");

// The algorithm structure is preserved while allowing customization
// Adding new report types only requires extending ReportGenerator
```