## Was ist ein Domänenmodell?

Ein Domänenmodell ist eine konzeptuelle Darstellung der wichtigsten Geschäftsobjekte (Entitäten) und ihrer Beziehungen in einer bestimmten Problemdomäne. Es zeigt **WAS** existiert, nicht **WIE** es implementiert wird.

## Grundlegende Elemente

### 1. Entitäten (Klassen)

- **Rechteckige Boxen** mit Klassenname im oberen Bereich
- Repräsentieren wichtige Geschäftsobjekte
- Beispiele aus deinem Diagramm: Betreiber, Besucher, Ausschreibung, Angebot

### 2. Attribute

- Listen innerhalb der Entitäten
- Beschreiben Eigenschaften der Entitäten
- Beispiele: Name, Adresse, Telefonnummer, E-Mail-Adresse

### 3. Beziehungen (Assoziationen)

- **Linien zwischen Entitäten**
- Zeigen wie Objekte miteinander in Verbindung stehen
- Wichtige Beziehungstypen in deinem Beispiel:
    - "hat" (Betreiber hat Mitglied)
    - "erstellt" (Auftraggeber erstellt Ausschreibung)
    - "schaut an" (Besucher schaut an Ausschreibung)
    - "gibt ab" (Handwerker gibt ab Angebot)

### 4. Multiplizitäten (Kardinalitäten)

- **Zahlen an den Enden der Beziehungslinien**
- Geben an, wie viele Objekte an einer Beziehung beteiligt sind
- Beispiele aus deinem Diagramm:
    - `1` = genau eins
    - `*` = null bis viele
    - `0..2` = null bis zwei
    - `1..*` = eins bis viele

## Spezialkonstrukte

### Enumerationen

- **Klassen mit «enumeration» Stereotyp**
- Listen von festen Werten
- Beispiele: Tätigkeitsgebiet, Ausschreibestatus, Angebotsstatus

### Abstrakte Klassen

- **Klassen mit «abstract» Stereotyp**
- Basis für andere Klassen, werden nicht direkt instanziiert
- Beispiel: Mitglied (Basis für verschiedene Mitgliedertypen)

### Vererbung/Generalisierung

- **Pfeil mit dreieckiger Spitze**
- "ist-ein" Beziehungen
- Beispiel: Auftraggeber und Handwerker erben von Mitglied

## Pfeil- und Linienarten (Visuelle Darstellung)

### Assoziationen (normale Beziehungen)

```
Klasse A ──────── Klasse B    (einfache Linie)
Klasse A ──────▶ Klasse B     (Linie mit Pfeil für Richtung)
```

### Vererbung/Generalisierung

```
Unterklasse ──────▷ Oberklasse    (Linie mit leerem Dreieck)
```

### Komposition (starke Abhängigkeit)

```
Teil ────────◆ Ganzes    (Linie mit gefülltem Diamant)
```

_Das Teil kann nicht ohne das Ganze existieren_

### Aggregation (schwache Abhängigkeit)

```
Teil ────────◇ Ganzes    (Linie mit leerem Diamant)
```

_Das Teil kann auch ohne das Ganze existieren_

### Abhängigkeiten

```
Klasse A ┈┈┈┈┈┈▶ Klasse B    (gestrichelte Linie mit Pfeil)
```

_Eine Klasse verwendet eine andere, aber ist nicht dauerhaft verbunden_

## Modellierungsstrategie für die Prüfung

### Schritt 1: Entitäten identifizieren

- Suche nach **Substantiven** in der Aufgabenstellung
- Frage: "Was sind die wichtigsten Geschäftsobjekte?"
- Beispiel: Personen, Dokumente, Prozesse, Orte

### Schritt 2: Attribute bestimmen

- Welche Eigenschaften beschreiben jede Entität?
- Nur relevante Attribute für die Domäne aufnehmen
- Beispiel: Für Person → Name, Adresse, Kontaktdaten

### Schritt 3: Beziehungen modellieren

- Wie hängen die Entitäten zusammen?
- Verwende aussagekräftige Beziehungsnamen
- Beispiel: "Kunde bestellt Produkt", "Mitarbeiter arbeitet in Abteilung"

### Schritt 4: Multiplizitäten festlegen

- Wie viele Objekte können an einer Beziehung beteiligt sein?
- Denke an Geschäftsregeln und Einschränkungen
- Beispiel: Ein Kunde kann mehrere Bestellungen haben (1 zu *)

### Schritt 5: Spezialisierungen prüfen

- Gibt es verschiedene Typen einer Entität?
- Verwende Vererbung für "ist-ein" Beziehungen
- Beispiel: Privatkunde und Geschäftskunde sind beides Kunden

## Häufige Fallen vermeiden

### ❌ Zu viele technische Details

- Keine Implementierungsdetails
- Keine Datenbank-spezifischen Konstrukte
- Fokus auf Geschäftslogik

### ❌ Fehlende Multiplizitäten

- Immer Kardinalitäten angeben
- Durchdenke die Geschäftsregeln

### ❌ Unklare Beziehungsnamen

- Verwende Verben oder Phrasen
- Beziehung sollte in beide Richtungen lesbar sein

### ❌ Zu viele oder zu wenige Entitäten

- Fokus auf wesentliche Geschäftsobjekte
- Nicht jedes Detail als separate Entität

## Tipps für die Prüfung

1. **Lies die Aufgabe sorgfältig** - identifiziere die Problemdomäne
2. **Markiere wichtige Substantive** - potenzielle Entitäten
3. **Achte auf Geschäftsregeln** - bestimmen Multiplizitäten
4. **Verwende konsistente Namenskonventionen** - deutsche/englische Begriffe
5. **Überprüfe die Vollständigkeit** - können alle Szenarien abgebildet werden?
6. **Halte es einfach** - nur das Nötige modellieren

## Checkliste vor Abgabe

- [ ] Alle wichtigen Entitäten identifiziert?
- [ ] Relevante Attribute pro Entität?
- [ ] Beziehungen mit aussagekräftigen Namen?
- [ ] Multiplizitäten überall angegeben?
- [ ] Vererbungen sinnvoll eingesetzt?
- [ ] Enumerationen für feste Wertelisten?
- [ ] Diagramm lesbar und übersichtlich?