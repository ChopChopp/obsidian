## Was ist ein Kommunikationsdiagramm?

Ein Kommunikationsdiagramm (früher **Kollaborationsdiagramm**) zeigt die **strukturelle Organisation von Objekten** und deren **Nachrichten-Austausch**. Im Gegensatz zum Sequenzdiagramm liegt der Fokus auf den **Verbindungen zwischen Objekten** statt auf der zeitlichen Abfolge.

## Grundaufbau

**Strukturelle Elemente:**

- **Objekte/Instanzen**: Rechtecke mit Objektname (`:Klassenname` oder `objektname:Klassenname`)
- **Verbindungslinien**: Zeigen Beziehungen zwischen Objekten
- **Nachrichten**: Pfeile entlang der Verbindungslinien
- **Sequenznummern**: Zeigen die zeitliche Reihenfolge der Nachrichten

## Objekt-Notation

### Anonyme Objekte

```
:GameWorld
:PhysicsEngine
```

- Doppelpunkt vor dem Klassennamen
- Kein spezifischer Objektname

### Benannte Objekte

```
engine:PhysicsEngine
world:GameWorld
```

- Objektname vor dem Doppelpunkt
- Klassenname nach dem Doppelpunkt

### Unbekannte Objekte

```
:????
```

- Fragezeichen für noch unbestimmte Objekte

## Verbindungslinien-Typen

|Linientyp|Darstellung|Bedeutung|
|---|---|---|
|Assoziation|`────────`|Normale Objektbeziehung|
|Selbstverbindung|`○`|Objekt kommuniziert mit sich selbst|
|Temporäre Verbindung|`┈┈┈┈┈┈`|Kurzzeitige Beziehung|

## Nachrichten-Notation

### Grundformat

```
sequenznummer: nachrichtenname(parameter)
```

### Einfache Nachricht

```
1: startStep(deltaTime)
```

- Sequenznummer zeigt Reihenfolge
- Methodenname mit Parametern

### Nachricht mit Rückgabewert

```
3: ok = update(deltaTime)
```

- Rückgabewert vor dem Gleichheitszeichen

### Bedingte Nachrichten

```
[bedingung] 2: simulate(deltaTime)
```

- Bedingung in eckigen Klammern

### Iterative Nachrichten (Schleifen)

```
2.1*[foreach shape]: simulate(deltaTime)
```

- Stern (*) zeigt Iteration an
- Bedingung in eckigen Klammern

## Sequenznummern-System

### Hierarchische Nummerierung

```
1: erste Nachricht
1.1: Unternachricht von 1
1.2: weitere Unternachricht von 1
2: zweite Hauptnachricht
2.1: Unternachricht von 2
2.1.1: Unter-Unternachricht von 2.1
```

### Bedeutung der Nummerierung

- **Ganze Zahlen** (1, 2, 3): Hauptsequenz
- **Dezimalstellen** (1.1, 1.2): Unteraufrufe/Verschachtelung
- **Reihenfolge**: Numerisch aufsteigend

## Pfeil-Typen

|Pfeiltyp|Darstellung|Bedeutung|
|---|---|---|
|Synchron|`────────▶`|Aufruf mit Warten auf Antwort|
|Asynchron|`────────▷`|Aufruf ohne Warten|
|Rückgabe|`◂┈┈┈┈┈┈┈`|Return-Wert (oft weggelassen)|

## Spezielle Notationen

### Selbstaufruf

```
:GameWorld ○──▶ 1: internalMethod()
```

- Kreis am Objekt für Selbstaufrufe

### Objekterstellung

```
1: <<create>> :PhysicsEngine
```

- `<<create>>` Stereotyp für neue Objekte

### Objektzerstörung

```
5: <<destroy>>
```

- `<<destroy>>` Stereotyp für Objektlöschung

## Praktisches Beispiel: Game Update Cycle

```
                    1: startStep(deltaTime)
:GameWorld ──────────────────────────────▶ :GameElement
     │                                           │
     │ 2: simulate(deltaTime)                    │
     └─────────────────────▶ :PhysicsEngine      │
                                    │            │
                                    │ 2.1*[foreach shape]: simulate(deltaTime)
                                    └─────────────▶ :PhysicsEngine::Shape
                                                        │
                                    ◂┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈│
                                                        │
                           2.1.1: handleCollision(otherShape) │
                                                        └──○
     │                                           │
     │ 3: ok = update(deltaTime)                │
     └───────────────────────────────────────▶ │
```

## Vergleich: Sequenz- vs. Kommunikationsdiagramm

|Aspekt|Sequenzdiagramm|Kommunikationsdiagramm|
|---|---|---|
|**Fokus**|Zeitliche Abfolge|Objektstruktur|
|**Layout**|Vertikal|Frei anordbar|
|**Zeitdarstellung**|Implizit (von oben nach unten)|Explizit (Nummern)|
|**Objektbeziehungen**|Weniger sichtbar|Klar erkennbar|
|**Übersichtlichkeit**|Bei vielen Nachrichten|Bei vielen Objekten|

## Schritt-für-Schritt Anleitung

1. **Objekte identifizieren** - welche Klassen/Instanzen sind beteiligt?
2. **Objekte positionieren** - räumlich sinnvoll anordnen
3. **Verbindungslinien zeichnen** - wer kommuniziert mit wem?
4. **Nachrichten einzeichnen** - mit Sequenznummern
5. **Reihenfolge überprüfen** - Nummern logisch vergeben
6. **Parameter und Rückgaben** ergänzen

## Erweitetes Beispiel: Online-Shop Checkout

```
:ShoppingCart ────1: checkout()────▶ :OrderProcessor
      │                                    │
      │                                    │ 1.1: validateItems()
      │                                    └─────────▶ :Inventory
      │                                              │
      │                                    ◂┈┈┈┈┈┈┈┈┈│
      │                                    │ 1.1.1: checkStock(item)
      │                                    │
      │         1.2: calculateTotal()      │
      │ ◂┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈ │
      │                                    │
      │                                    │ 1.3: processPayment(amount)
      │                                    └─────────▶ :PaymentGateway
      │                                              │
      │                                    ◂┈┈┈┈┈┈┈┈┈│
      │                                    │ 1.3.1: confirmPayment()
      │                                    │
      │ 2: confirmOrder(orderNumber)       │
      │ ◂┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈ │
```

## Häufige Fehler vermeiden

- **Sequenznummern vergessen** - jede Nachricht braucht eine Nummer
- **Verbindungslinien fehlen** - Objekte müssen verbunden sein für Kommunikation
- **Unlogische Nummerierung** - Reihenfolge muss stimmen
- **Zu viele Objekte** - bei Übersichtlichkeitsproblemen aufteilen
- **Inkonsistente Pfeiltypen** - synchron/asynchron klar unterscheiden

## Wann Kommunikationsdiagramm verwenden?

**Vorteile:**

- **Objektbeziehungen** werden deutlich
- **Freie räumliche Anordnung** möglich
- **Kompakter** bei wenigen langen Sequenzen
- **Architektur-Fokus** statt Zeit-Fokus

**Nachteile:**

- **Zeitliche Abfolge** weniger intuitiv
- **Komplexe Sequenzen** schwer lesbar
- **Viele Nachrichten** werden unübersichtlich

## Checkliste für gute Kommunikationsdiagramme

- [ ] Alle relevanten Objekte eingezeichnet
- [ ] Verbindungslinien zwischen kommunizierenden Objekten
- [ ] Sequenznummern logisch und vollständig
- [ ] Nachrichtennamen aussagekräftig
- [ ] Parameter und Rückgaben wo relevant
- [ ] Räumliche Anordnung unterstützt Verständnis
- [ ] Nicht zu viele Objekte in einem Diagramm
- [ ] Konsistente Notation verwendet

## Fazit

Kommunikationsdiagramme eignen sich besonders für die **Darstellung von Objektstrukturen** und deren Zusammenspiel. Sie ergänzen Sequenzdiagramme, indem sie den **strukturellen Aspekt** der Objektkommunikation betonen und sind ideal für **Architektur-Reviews** und **Design-Diskussionen**.