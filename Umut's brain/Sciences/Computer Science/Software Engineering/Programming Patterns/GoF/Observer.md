## Core Principal

Define a one-to-many dependency between objects so that when one object changes state, all its dependents are notified and updated automatically. Promotes loose coupling between subjects and observers.

## The Problem It Solves

- Need to notify multiple objects about state changes
- Want to avoid tight coupling between objects that need to stay synchronized
- Number of dependent objects is unknown or changes dynamically
- Need to broadcast notifications without knowing who receives them

## The Solution

Create a subject interface that maintains a list of observers and notifies them of changes. Observers register with subjects and implement an update method that gets called when the subject's state changes.

## Quick Test

Ask yourself: "Do I have an object whose state changes need to be communicated to multiple other objects?" If you need one-to-many notifications with loose coupling, use Observer.

## Examples

### Bad

```java
// Tightly coupled notification system
class WeatherStation {
    private float temperature;
    private float humidity;
    private float pressure;
    
    // Hard-coded dependencies on specific displays
    private CurrentConditionsDisplay currentDisplay;
    private StatisticsDisplay statisticsDisplay;
    private ForecastDisplay forecastDisplay;
    
    public WeatherStation() {
        // Tight coupling to concrete classes
        this.currentDisplay = new CurrentConditionsDisplay();
        this.statisticsDisplay = new StatisticsDisplay();
        this.forecastDisplay = new ForecastDisplay();
    }
    
    public void setMeasurements(float temperature, float humidity, float pressure) {
        this.temperature = temperature;
        this.humidity = humidity;
        this.pressure = pressure;
        
        // Must manually notify each display - violates Open/Closed Principle
        currentDisplay.update(temperature, humidity, pressure);
        statisticsDisplay.update(temperature, humidity, pressure);
        forecastDisplay.update(temperature, humidity, pressure);
        
        // Adding new displays requires modifying this method!
    }
}

class CurrentConditionsDisplay {
    public void update(float temperature, float humidity, float pressure) {
        System.out.println("Current: " + temperature + "°F, " + humidity + "% humidity");
    }
}

class StatisticsDisplay {
    public void update(float temperature, float humidity, float pressure) {
        System.out.println("Statistics updated with new data");
    }
}

class ForecastDisplay {
    public void update(float temperature, float humidity, float pressure) {
        System.out.println("Forecast updated");
    }
}
```

### Good

```java
// Observer interface
interface Observer {
    void update(float temperature, float humidity, float pressure);
}

// Subject interface
interface Subject {
    void registerObserver(Observer observer);
    void removeObserver(Observer observer);
    void notifyObservers();
}

// Concrete subject
class WeatherData implements Subject {
    private List<Observer> observers;
    private float temperature;
    private float humidity;
    private float pressure;
    
    public WeatherData() {
        observers = new ArrayList<>();
    }
    
    @Override
    public void registerObserver(Observer observer) {
        observers.add(observer);
    }
    
    @Override
    public void removeObserver(Observer observer) {
        observers.remove(observer);
    }
    
    @Override
    public void notifyObservers() {
        for (Observer observer : observers) {
            observer.update(temperature, humidity, pressure);
        }
    }
    
    public void setMeasurements(float temperature, float humidity, float pressure) {
        this.temperature = temperature;
        this.humidity = humidity;
        this.pressure = pressure;
        measurementsChanged();
    }
    
    private void measurementsChanged() {
        notifyObservers();
    }
}

// Concrete observers
class CurrentConditionsDisplay implements Observer {
    private float temperature;
    private float humidity;
    
    @Override
    public void update(float temperature, float humidity, float pressure) {
        this.temperature = temperature;
        this.humidity = humidity;
        display();
    }
    
    public void display() {
        System.out.println("Current conditions: " + temperature + "°F degrees and " + humidity + "% humidity");
    }
}

class StatisticsDisplay implements Observer {
    private List<Float> temperatures = new ArrayList<>();
    
    @Override
    public void update(float temperature, float humidity, float pressure) {
        temperatures.add(temperature);
        display();
    }
    
    public void display() {
        float avg = (float) temperatures.stream().mapToDouble(Float::doubleValue).average().orElse(0.0);
        System.out.println("Avg/Max/Min temperature = " + avg + "/" + 
                          Collections.max(temperatures) + "/" + Collections.min(temperatures));
    }
}

class ForecastDisplay implements Observer {
    private float currentPressure = 29.92f;
    private float lastPressure;
    
    @Override
    public void update(float temperature, float humidity, float pressure) {
        lastPressure = currentPressure;
        currentPressure = pressure;
        display();
    }
    
    public void display() {
        System.out.print("Forecast: ");
        if (currentPressure > lastPressure) {
            System.out.println("Improving weather on the way!");
        } else if (currentPressure == lastPressure) {
            System.out.println("More of the same");
        } else {
            System.out.println("Watch out for cooler, rainy weather");
        }
    }
}

// Usage - loose coupling and easy extensibility
WeatherData weatherData = new WeatherData();

CurrentConditionsDisplay currentDisplay = new CurrentConditionsDisplay();
StatisticsDisplay statisticsDisplay = new StatisticsDisplay();
ForecastDisplay forecastDisplay = new ForecastDisplay();

// Dynamic registration
weatherData.registerObserver(currentDisplay);
weatherData.registerObserver(statisticsDisplay);
weatherData.registerObserver(forecastDisplay);

weatherData.setMeasurements(80, 65, 30.4f);
weatherData.setMeasurements(82, 70, 29.2f);

// Can remove observers dynamically
weatherData.removeObserver(forecastDisplay);
weatherData.setMeasurements(78, 90, 29.2f);
```