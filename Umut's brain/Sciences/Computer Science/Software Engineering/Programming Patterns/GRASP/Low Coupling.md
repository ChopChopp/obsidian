# Core principal
*Minimize dependencies between classes. Depend on abstractions, not concrete implementations.*

# The problem it solves
- **High coupling** - classes knowing too much about each other
- **Change fragility** - one change breaks many classes
- **Testing difficulty** - can't test classes in isolation
- **Reuse impossibility** - classes come with baggage of dependencies

# Key strategies for Low Coupling
## 1. Depend on Abstractions

**❌ BAD:** ```
```java
// EmailSender == CONCRETE IMPLEMENTATION!
class OrderService { private EmailSender emailSender; }
```
**✅ GOOD:** 
```java
// NotificationService == INTERFACE!
class OrderService { private NotificationService notifier; }
```
## 2. Use Dependency Injection

**❌ BAD:** ```
```java
// create inside class!
emailSender = new EmailSender();
```
**✅ GOOD:** 
```java
// pass in constructor!
OrderService(NotificationService notifier)
```

## 3. Define clear interfaces

**❌ BAD:** Know implementation details of other classes
**✅ GOOD:** Interact through well-defined contracts
# Benefits
- **Flexibility** - easy to change implementations
- **Testability** - mock dependencies for unit tests
- **Maintainability** - changes don't cascade
- **Resuability** - classes work with different implementations
# Quick test
Count concrete class names in your constructor parameters. The fewer, the better.
# Examples
> Bad

``` java
// Playlist depends on concrete classes
class Playlist {
    private AudioPlayer audioPlayer;    // Concrete dependency!
    private DatabaseManager db;         // Concrete dependency!
    private Logger logger;              // Concrete dependency!
    
    public void addSong(Song song) {
        songs.add(song);
        
        // WRONG: Knows AudioPlayer's internal methods
        audioPlayer.refreshCurrentPlaylist();
        
        // WRONG: Knows DB's specific SQL format
        db.executeUpdate("INSERT INTO playlist_songs VALUES...");
    }
}
```

> Good

``` java
// Playlist depends on abstractions
class Playlist {
    private PlaybackController controller;  // Interface dependency!
    private DataPersistence persistence;    // Interface dependency!
    private EventLogger logger;             // Interface dependency!
    
    public void addSong(Song song) {
        songs.add(song);
        
        // RIGHT: Uses abstract interfaces
        persistence.savePlaylistSong(name, song.getId());
        logger.logPlaylistAction("SONG_ADDED", name, song.getTitle());
    }
}
```
