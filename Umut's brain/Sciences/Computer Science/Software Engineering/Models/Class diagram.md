# Klassendiagramm

## Was ist ein Klassendiagramm?

Ein Klassendiagramm zeigt die **statische Struktur** eines Systems durch **Klassen**, deren **Attribute**, **Methoden** und die **Beziehungen** zwischen ihnen. Es ist das wichtigste Strukturdiagramm in der objektorientierten Modellierung.

## Grundaufbau einer Klasse

```
┌─────────────────────┐
│     Klassenname     │  ← Klassenname (fett, zentriert)
├─────────────────────┤
│ - attribut1: Typ    │  ← Attribute
│ + attribut2: Typ    │
│ # attribut3: Typ    │
├─────────────────────┤
│ + methode1(): Typ   │  ← Methoden/Operationen
│ - methode2(p: Typ)  │
│ # methode3(): void  │
└─────────────────────┘
```

## Sichtbarkeits-Modifikatoren

|Symbol|Sichtbarkeit|Bedeutung|
|---|---|---|
|`+`|**public**|Von überall zugreifbar|
|`-`|**private**|Nur innerhalb der Klasse|
|`#`|**protected**|In Klasse und Unterklassen|
|`~`|**package**|Innerhalb des Pakets|

## Attribut-Notation

### Vollständige Syntax

```
sichtbarkeit name: typ [multiplizität] = defaultwert {eigenschaft}
```

### Beispiele

```
+ name: String
- age: int = 0
# balance: double [0..1]
+ items: List<Item> [*]
- isActive: boolean = true {readOnly}
```

### Besondere Attribute

```
+ CONSTANT: int = 100        // Konstante (GROSSBUCHSTABEN)
- _privateVar: String        // Private (oft mit _)
+ /derivedAge: int           // Abgeleitetes Attribut (/)
```

## Methoden-Notation

### Vollständige Syntax

```
sichtbarkeit name(parameter: typ): rückgabetyp {eigenschaft}
```

### Beispiele

```
+ getName(): String
+ setAge(newAge: int): void
- calculateTotal(items: List<Item>): double
# validateInput(input: String): boolean
+ toString(): String {query}
+ save(): void {abstract}
```

### Besondere Methoden

```
+ Person()                   // Konstruktor (kein Rückgabetyp)
+ ~Person()                 // Destruktor (mit ~)
+ getName(): String {static} // Statische Methode
+ process(): void {abstract} // Abstrakte Methode
```

## Klassentypen und Stereotypen

### Abstrakte Klassen

```
┌─────────────────────┐
│    <<abstract>>     │  ← Stereotyp
│     Vehicle         │  oder: kursiv schreiben
├─────────────────────┤
│ + speed: double     │
├─────────────────────┤
│ + move(): void      │
│ + stop(): void      │
└─────────────────────┘
```

### Interfaces

```
┌─────────────────────┐
│   <<interface>>     │
│    Drawable         │
├─────────────────────┤
│                     │  ← Keine Attribute (meist)
├─────────────────────┤
│ + draw(): void      │
│ + erase(): void     │
└─────────────────────┘
```

### Weitere Stereotypen

```
<<enumeration>>     // Aufzählung
<<utility>>         // Hilfsklasse
<<entity>>          // Entitätsklasse
<<boundary>>        // Grenzklasse
<<control>>         // Steuerungsklasse
<<exception>>       // Exception-Klasse
```

## Beziehungstypen

### 1. Assoziation

```
Kunde ────────────────── Bestellung
      "bestellt"    "wird bestellt von"
```

- **Durchgezogene Linie**
- Normale Beziehung zwischen Klassen
- Optional: Rollennamen und Beschriftung

### 2. Gerichtete Assoziation

```
Kunde ──────────────────▶ Bestellung
      "kennt"
```

- **Pfeil** zeigt Navigationsrichtung
- Kunde kennt Bestellung, aber nicht umgekehrt

### 3. Aggregation ("hat-ein")

```
Auto ◇────────────────── Motor
```

- **Leerer Diamant** beim "Ganzen"
- Schwache Beziehung - Teile können ohne Ganzes existieren

### 4. Komposition ("besteht-aus")

```
Haus ◆────────────────── Zimmer
```

- **Gefüllter Diamant** beim "Ganzen"
- Starke Beziehung - Teile werden mit Ganzem zerstört

### 5. Vererbung/Generalisierung

```
Fahrzeug
    △
    │
    │
  Auto
```

- **Leerer Dreieckspfeil** zum Obertyp
- "ist-ein" Beziehung

### 6. Realisierung/Implementation

```
<<interface>>
Drawable
    △
    ┊
    ┊
 Circle
```

- **Gestrichelter Pfeil** mit leerem Dreieck
- Klasse implementiert Interface

### 7. Abhängigkeit

```
Controller ┈┈┈┈┈┈┈┈┈▶ Service
           <<uses>>
```

- **Gestrichelter Pfeil**
- Schwache, temporäre Beziehung

## Multiplizitäten

|Notation|Bedeutung|
|---|---|
|`1`|Genau eins|
|`0..1`|Null oder eins|
|`*` oder `0..*`|Beliebig viele|
|`1..*`|Mindestens eins|
|`2..5`|Zwischen 2 und 5|
|`3`|Genau drei|

### Beispiele mit Multiplizitäten

```
Kunde ────────────────── Bestellung
  1                         *
"ein Kunde kann mehrere Bestellungen haben"

Person ────────────────── Auto
  1                      0..1
"eine Person kann kein oder ein Auto besitzen"
```

## Erweiterte Notationen

### Assoziationsklassen

```
Student ─────────────── Kurs
           │
           │
      ┌─────────┐
      │Einschrei│
      │  bung   │
      ├─────────┤
      │+ datum  │
      │+ note   │
      └─────────┘
```

### Qualifizierte Assoziation

```
Bank ┌─────────┐────── Konto
     │KontoNr  │
     └─────────┘
```

### Selbstreferenz

```
      ┌─────────┐
   ┌──│ Person  │
   │  ├─────────┤
   │  │+ name   │
   │  └─────────┘
   │       │
   └───────┘
   "verheiratet mit"
```

## Praktisches Beispiel: E-Commerce System

```
┌─────────────────────┐    1    ┌─────────────────────┐
│      Customer       │◇────────│    ShoppingCart     │
├─────────────────────┤         ├─────────────────────┤
│- customerId: String │         │- items: List<Item>  │
│+ name: String       │         │- total: double      │
│+ email: String      │         ├─────────────────────┤
├─────────────────────┤         │+ addItem(): void    │
│+ register(): void   │         │+ removeItem(): void │
│+ login(): boolean   │         │+ calculateTotal()   │
└─────────────────────┘         └─────────────────────┘
            │                              │
            │ 1                            │ *
            │                              │
            ▼                              ▼
    ┌─────────────────────┐        ┌─────────────────────┐
    │       Order         │        │      Product        │
    ├─────────────────────┤        ├─────────────────────┤
    │- orderId: String    │        │- productId: String  │
    │- orderDate: Date    │        │+ name: String       │
    │- status: String     │        │+ price: double      │
    ├─────────────────────┤        │+ description: String│
    │+ processOrder()     │        ├─────────────────────┤
    │+ cancel(): boolean  │        │+ getDetails(): String│
    └─────────────────────┘        │+ updatePrice(): void│
                                   └─────────────────────┘
```

## Vererbungshierarchie Beispiel

```
            ┌─────────────────────┐
            │    <<abstract>>     │
            │      Vehicle        │
            ├─────────────────────┤
            │# speed: double      │
            │# fuel: double       │
            ├─────────────────────┤
            │+ start(): void      │
            │+ stop(): void       │
            │+ refuel(): void     │
            └─────────────────────┘
                       △
                       │
            ┌──────────┼──────────┐
            │                     │
    ┌─────────────────────┐ ┌─────────────────────┐
    │        Car          │ │       Truck         │
    ├─────────────────────┤ ├─────────────────────┤
    │- doors: int         │ │- loadCapacity: int  │
    │- isConvertible: bool│ │- trailer: boolean   │
    ├─────────────────────┤ ├─────────────────────┤
    │+ openDoors(): void  │ │+ loadCargo(): void  │
    │+ lockCar(): void    │ │+ unloadCargo(): void│
    └─────────────────────┘ └─────────────────────┘
```

## Interface-Implementation Beispiel

```
    ┌─────────────────────┐
    │   <<interface>>     │
    │     Drawable        │
    ├─────────────────────┤
    │+ draw(): void       │
    │+ move(x,y): void    │
    └─────────────────────┘
               △
               ┊ <<implements>>
               ┊
    ┌─────────────────────┐
    │      Circle         │
    ├─────────────────────┤
    │- radius: double     │
    │- center: Point      │
    ├─────────────────────┤
    │+ draw(): void       │
    │+ move(x,y): void    │
    │+ calculateArea()    │
    └─────────────────────┘
```

## Packages und Namespaces

```
┌─────────── com.company.model ──────────────┐
│  ┌─────────────────────┐ ┌────────────────┐│
│  │      Customer       │ │    Product     ││
│  └─────────────────────┘ └────────────────┘│
└────────────────────────────────────────────┘

┌─────────── com.company.service ────────────┐
│  ┌─────────────────────┐                  │
│  │   CustomerService   │                  │
│  └─────────────────────┘                  │
└────────────────────────────────────────────┘
```

## Constraints und Notizen

```
┌─────────────────────┐
│      Person         │
├─────────────────────┤     ┌─────────────────────┐
│+ age: int           │────▶│ {age >= 0}          │
│+ name: String       │     │                     │
├─────────────────────┤     │ Constraint:         │
│+ setAge(int): void  │     │ Age must be         │
└─────────────────────┘     │ non-negative        │
                            └─────────────────────┘
```

## Häufige Fehler vermeiden

- **Falsche Pfeilrichtungen** bei Vererbung (Pfeil zeigt zum Elternteil)
- **Verwechslung** von Aggregation und Komposition
- **Fehlende Multiplizitäten** bei Assoziationen
- **Zu viele Details** in frühen Entwurfsphasen
- **Inkonsistente Sichtbarkeiten** - private Attribute sollten private bleiben
- **Zirkuläre Abhängigkeiten** vermeiden
- **Überladene Diagramme** - bei Bedarf aufteilen

## Entwicklungsphasen

### Konzeptionelles Klassendiagramm

- **Nur Klassen und Beziehungen**
- **Keine Implementierungsdetails**
- **Domänen-fokussiert**

### Spezifikations-Klassendiagramm

- **Vollständige Attribut- und Methodensignaturen**
- **Sichtbarkeiten definiert**
- **Constraints spezifiziert**

### Implementierungs-Klassendiagramm

- **Alle Implementierungsdetails**
- **Plattformspezifische Typen**
- **Optimierungen sichtbar**

## Checkliste für gute Klassendiagramme

- [ ] Klassen repräsentieren echte Konzepte der Domäne
- [ ] Beziehungstypen korrekt gewählt
- [ ] Multiplizitäten angegeben wo relevant
- [ ] Sichtbarkeiten konsistent vergeben
- [ ] Vererbungshierarchien logisch aufgebaut
- [ ] Keine zirkulären Abhängigkeiten
- [ ] Angemessener Detailgrad für Zielgruppe
- [ ] Große Diagramme in Packages unterteilt
- [ ] Stereotypen sinnvoll eingesetzt

## Fazit

Klassendiagramme sind das **Herzstück der objektorientierten Modellierung**. Sie entwickeln sich von groben konzeptionellen Entwürfen zu detaillierten Implementierungsvorlagen. Die richtige Balance zwischen **Vollständigkeit** und **Übersichtlichkeit** ist entscheidend für ihren Erfolg als Kommunikationsmittel.