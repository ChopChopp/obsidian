## Was sind Use Cases?

Use Cases beschreiben die Interaktion zwischen einem **Akteur** (Actor) und einem **System**, um ein bestimmtes **Ziel** zu erreichen. Sie zeigen **WAS** das System tun soll, nicht **WIE** es implementiert wird.

## Grundlegende Elemente

### Akteure (Actors)

- **Personen, Systeme oder Organisationen** außerhalb des Systems
- Initiieren oder reagieren auf Use Cases
- Beispiele: Kunde, Administrator, externes System

### Use Cases

- **Funktionalitäten** des Systems aus Sicht der Akteure
- Beschreiben ein **messbares Geschäftsziel**
- Beginnen meist mit Verben: "Buch ausleihen", "Rechnung erstellen"

### System Boundary

- **Grenze** zwischen System und Umgebung
- Zeigt was **innerhalb** und **außerhalb** des Systems liegt

## Die drei Use Case Formate

## 1. Brief Use Case (Kurz-Format)

### Zweck

- **Schnelle Übersicht** über Systemfunktionalitäten
- Erste Sammlung von Use Cases
- **Ein bis zwei Sätze** pro Use Case

### Format

```
Use Case: [Name]
Beschreibung: [Kurze Zusammenfassung in 1-2 Sätzen]
```

### Beispiel

```
Use Case: Buch ausleihen
Beschreibung: Ein Bibliotheksmitglied leiht ein verfügbares Buch aus der Bibliothek aus. Das System registriert die Ausleihe und setzt das Rückgabedatum.

Use Case: Artikel bestellen
Beschreibung: Ein Kunde wählt Artikel aus dem Online-Shop aus und schließt eine Bestellung ab.
```

### Wann verwenden?

- **Brainstorming-Phase** für Use Case Sammlung
- **Stakeholder-Kommunikation** (nicht-technische Zielgruppe)
- **Projektplanung** und Aufwandschätzung

---

## 2. Casual Use Case (Informell)

### Zweck

- **Strukturierte Beschreibung** mit mehr Details
- **Hauptszenario** wird durchgespielt
- **Paragraphen-Form** mit mehreren Schritten

### Format

```
Use Case: [Name]
Akteur: [Primärer Akteur]
Ziel: [Was erreicht werden soll]

Szenario:
[Beschreibung des Hauptablaufs in Paragraphen-Form mit mehreren Schritten]
```

### Beispiel

```
Use Case: Buch ausleihen
Akteur: Bibliotheksmitglied
Ziel: Ein Buch für eine bestimmte Zeit ausleihen

Szenario:
Das Bibliotheksmitglied gibt seine Mitgliedsnummer ein und authentifiziert sich. 
Es sucht nach dem gewünschten Buch über die Suchfunktion und wählt es aus. 
Das System prüft die Verfügbarkeit und ob das Mitglied noch Bücher ausleihen darf. 
Wenn alles in Ordnung ist, wird das Buch ausgeliehen, die Ausleihe registriert und 
ein Rückgabedatum (14 Tage) festgelegt. Das Mitglied erhält eine Bestätigung.
```

### Wann verwenden?

- **Requirements-Engineering** Phase
- **Entwicklungsplanung** und -vorbereitung
- **Kommunikation** zwischen Business und Development

---

## 3. Fully Dressed Use Case (Vollständig)

### Zweck

- **Detaillierte Spezifikation** für Entwicklung
- **Alle Szenarien** und Ausnahmen abgedeckt
- **Strukturierte Vorlage** mit allen Elementen

### Format

```
Use Case: [Name]
Akteur: [Primärer Akteur]
Stakeholder: [Alle betroffenen Personen/Systeme]
Vorbedingungen: [Was muss erfüllt sein?]
Nachbedingungen: [Was ist nach erfolgreichem Abschluss erreicht?]
Auslöser: [Was startet den Use Case?]

Hauptszenario:
1. [Schritt 1]
2. [Schritt 2]
3. [Schritt 3]
...

Alternative Szenarien:
[Nummer]a. [Alternative bei Schritt X]
1. [Schritt 1 der Alternative]
2. [Schritt 2 der Alternative]
...

Ausnahmeszenarien:
[Nummer]b. [Ausnahme bei Schritt X]
1. [Schritt 1 der Ausnahmebehandlung]
2. [Schritt 2 der Ausnahmebehandlung]
...

Nicht-funktionale Anforderungen:
- Performance: [z.B. Antwortzeit < 2 Sekunden]
- Usability: [z.B. Bedienbarkeit ohne Schulung]
- Security: [z.B. Verschlüsselung erforderlich]
```

### Vollständiges Beispiel

```
Use Case: Buch ausleihen
Akteur: Bibliotheksmitglied
Stakeholder: Bibliotheksmitglied (will Buch lesen), Bibliothek (will Bücher verwalten)
Vorbedingungen: 
- Mitglied ist registriert und hat gültige Mitgliedschaft
- Buch ist im System erfasst und verfügbar
Nachbedingungen: 
- Buch ist dem Mitglied zugeordnet
- Rückgabedatum ist gesetzt
- Ausleihe ist im System dokumentiert
Auslöser: Mitglied möchte ein Buch ausleihen

Hauptszenario:
1. Mitglied gibt Mitgliedsnummer ein
2. System authentifiziert Mitglied
3. Mitglied sucht nach gewünschtem Buch
4. System zeigt Suchergebnisse an
5. Mitglied wählt Buch aus
6. System prüft Verfügbarkeit des Buchs
7. System prüft Ausleihberechtigung des Mitglieds
8. System registriert Ausleihe mit Rückgabedatum (14 Tage)
9. System bestätigt erfolgreiche Ausleihe
10. Mitglied erhält Ausleihinformation

Alternative Szenarien:
3a. Buch ist über Barcode-Scanner verfügbar
1. Mitglied scannt Barcode des Buchs
2. Weiter bei Schritt 6

7a. Mitglied hat bereits maximale Anzahl Bücher ausgeliehen
1. System zeigt Fehlermeldung an
2. System schlägt Rückgabe anderer Bücher vor
3. Use Case endet

Ausnahmeszenarien:
2b. Authentifizierung fehlgeschlagen
1. System zeigt Fehlermeldung an
2. System bietet erneute Eingabe an
3. Nach 3 Versuchen wird Mitglied gesperrt
4. Use Case endet

6b. Buch ist nicht verfügbar
1. System zeigt Status an (ausgeliehen/reserviert)
2. System bietet Reservierung an
3. Use Case wechselt zu "Buch reservieren"

Nicht-funktionale Anforderungen:
- Performance: Suche und Ausleihe < 3 Sekunden
- Usability: Bedienung ohne Schulung möglich
- Verfügbarkeit: System verfügbar während Öffnungszeiten
- Security: Mitgliedsdaten verschlüsselt übertragen
```

### Wann verwenden?

- **Entwicklungsphase** - detaillierte Implementierung
- **Testing** - Testfall-Erstellung
- **Komplexe Use Cases** mit vielen Varianten

---

## Modellierungsstrategie für die Prüfung

### Schritt 1: Akteure identifizieren

- Wer interagiert mit dem System?
- Welche externen Systeme sind beteiligt?
- Beispiel: Kunde, Administrator, Zahlungssystem

### Schritt 2: Use Cases sammeln

- Was wollen die Akteure erreichen?
- Welche Geschäftsziele verfolgen sie?
- Beginne mit Brief Use Cases

### Schritt 3: Format wählen

- **Brief**: Für Übersicht und Kommunikation
- **Casual**: Für Requirements und Planung
- **Fully Dressed**: Für kritische/komplexe Funktionen

### Schritt 4: Detaillierungsgrad anpassen

- Je wichtiger der Use Case, desto detaillierter
- Kernfunktionalitäten vollständig ausarbeiten
- Weniger kritische Use Cases casual belassen

### Schritt 5: Use Case Diagramm erstellen

- Visualisierung der Beziehungen
- Akteure außerhalb der System-Grenze
- Include/Extend Beziehungen bei Bedarf

## Häufige Fallen vermeiden

### ❌ Zu technisch

- Use Cases beschreiben **Geschäftsprozesse**, nicht Implementation
- Vermeide technische Details wie "SQL Query ausführen"

### ❌ Zu granular

- Use Cases sollten **messbare Geschäftsziele** erreichen
- "Button klicken" ist kein Use Case, "Bestellung aufgeben" schon

### ❌ Fehlende Akteure

- Jeder Use Case braucht mindestens einen **primären Akteur**
- Akteure sind immer **außerhalb** des Systems

### ❌ Unklare Systemgrenze

- Was gehört zum System, was nicht?
- System-Grenze muss klar definiert sein

## Tipps für die Prüfung

1. **Lies die Aufgabe genau** - identifiziere Akteure und Systemgrenze
2. **Starte mit Brief Use Cases** - sammle alle Funktionalitäten
3. **Wähle das richtige Format** - je nach Aufgabenstellung
4. **Verwende Aktivverben** - "Kunde bestellt Artikel"
5. **Denke an Ausnahmen** - bei Fully Dressed Use Cases
6. **Halte Business-Sicht** - nicht technische Implementation

## Use Case Diagramm - Modellierungsaspekte

### Grundelemente

- **Akteure**: Kreise oder Strichmännchen (außerhalb System)
- **Use Cases**: Ovale/Rechtecke (innerhalb System)
- **System Boundary**: Rechteck um alle Use Cases
- **Assoziationen**: Einfache Linien zwischen Akteur und Use Case

### Erweiterte Beziehungen

#### Include-Beziehungen (<<include>>)

```
Use Case A ----<<include>>----> Use Case B
```

- **Pflichtige Einbindung**: A beinhaltet IMMER B
- Beispiel: "Buch ausleihen" includes "Authentifizierung"

#### Extend-Beziehungen (<<extend>>)

```
Use Case B ----<<extend>>----> Use Case A
```

- **Optionale Erweiterung**: B erweitert A unter bestimmten Bedingungen
- Beispiel: "Buch bewerten" extends "Buch zurückgeben"

#### Generalisierung zwischen Akteuren

```
Spezieller Akteur ----▷ Allgemeiner Akteur
```

- Vererbung von Rechten und Verantwortlichkeiten
- Beispiel: "Administrator" erbt von "Benutzer"

#### Generalisierung zwischen Use Cases

```
Spezieller Use Case ----▷ Allgemeiner Use Case
```

- Spezialisierung von allgemeinen Funktionen
- Beispiel: "Online zahlen" und "Bar zahlen" erben von "Zahlen"

### Visualisierungsregeln

- **Akteure**: Immer außerhalb der Systemgrenze
- **Externe Systeme**: Als spezielle Akteure (oft rechteckig)
- **Include**: Gestrichelte Pfeile, zeigen auf wiederverwendbare Use Cases
- **Extend**: Gestrichelte Pfeile, zeigen auf erweiterte Use Cases
- **Systemgrenze**: Klar erkennbare Abgrenzung

## Checkliste vor Abgabe

- [ ] Alle wichtigen Akteure identifiziert?
- [ ] Use Cases haben klare Geschäftsziele?
- [ ] Richtiges Format für Aufgabenstellung gewählt?
- [ ] Systemgrenze klar definiert?
- [ ] Bei Fully Dressed: Alle Szenarien durchdacht?
- [ ] Include/Extend Beziehungen korrekt verwendet?
- [ ] Akteure außerhalb, Use Cases innerhalb der Systemgrenze?
- [ ] Sprache verständlich und nicht-technisch?
- [ ] Vorbedingungen und Nachbedingungen sinnvoll?