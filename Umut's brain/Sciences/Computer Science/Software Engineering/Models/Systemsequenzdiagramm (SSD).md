## Was ist ein SSD?

Ein Systemsequenzdiagramm zeigt die **zeitliche Abfolge von Nachrichten** zwischen einem Akteur und dem System während eines Use Cases. Es ist eine **Black-Box-Sicht** - wir sehen nur, was von außen mit dem System passiert.

## Grundaufbau

**Vertikale Struktur:**

- **Akteur** (links): Strichmännchen oder Rechteck
- **System** (rechts): Rechteck mit Systemname
- **Lebenslinien**: Gestrichelte vertikale Linien nach unten
- **Zeit fließt von oben nach unten**

## Pfeil- und Linienarten

### 1. Synchrone Nachrichten

```
Akteur ────────────────> System
       nachrichtenName()
```

- **Durchgezogener Pfeil** mit **geschlossener Spitze**
- Sender wartet auf Antwort
- Meist für Methodenaufrufe

### 2. Asynchrone Nachrichten

```
Akteur ────────────────▷ System
       ereignisName
```

- **Durchgezogener Pfeil** mit **offener Spitze**
- Sender wartet nicht auf Antwort
- Für Events, Signale

### 3. Antwortnachrichten

```
Akteur ◂┈┈┈┈┈┈┈┈┈┈┈┈┈┈ System
       rückgabewert
```

- **Gestrichelter Pfeil** zurück
- Zeigt Rückgabewerte oder Bestätigungen
- Optional, oft weggelassen wenn offensichtlich

### 4. Selbstaufruf

```
System ┐
       │ internalMethod()
       └──────────────────▷
```

- System ruft sich selbst auf
- Schleife an der Lebenslinie

## Weitere Notationselemente

### Aktivierungsbalken

```
Akteur     System
  │          ┃
  │          ┃ ← Aktivierungsbalken
  │          ┃   (zeigt aktive Zeit)
  │          │
```

### Bedingungen/Guards

```
[bedingung] nachricht()
```

- In eckigen Klammern vor dem Nachrichtennamen

### Schleifen

```
loop [bedingung]
    nachricht()
end
```

### Alternative Pfade

```
alt [bedingung1]
    nachricht1()
else [bedingung2]  
    nachricht2()
end
```

### Optionale Nachrichten

```
opt [bedingung]
    nachricht()
end
```

## Detaillierte Pfeilnotation

|Pfeiltyp|Darstellung|Bedeutung|Verwendung|
|---|---|---|---|
|Synchron|`────────>`|Warten auf Antwort|Methodenaufrufe|
|Asynchron|`────────▷`|Kein Warten|Events, Signale|
|Rückgabe|`◂┈┈┈┈┈┈┈`|Antwort/Return|Rückgabewerte|
|Selbstaufruf|`┐└──────▷`|Rekursion|Interne Verarbeitung|

## Linientypen

|Linientyp|Darstellung|Bedeutung|
|---|---|---|
|Durchgezogen|`────────`|Aktive Nachricht|
|Gestrichelt|`┈┈┈┈┈┈┈┈`|Rückgabe/Antwort|
|Lebenslinie|`│` (vertikal)|Existenz über Zeit|

## Schritt-für-Schritt Anleitung

1. **Use Case analysieren** - welche Schritte macht der Akteur?
2. **Akteur und System zeichnen** mit Lebenslinien
3. **Erste Nachricht** vom Akteur zum System
4. **Chronologisch fortfahren** - jede Interaktion als Pfeil
5. **Rückgaben einzeichnen** (optional)
6. **Bedingungen und Schleifen** ergänzen

## Praktisches Beispiel: Login-Prozess

```
Benutzer                    System
    │                         │
    │──eingabeCredentials()──▷ │
    │                         │
    │◂┈┈┈┈validierungResult┈┈ │
    │                         │
    │ [valid]                 │
    │──anmelden()────────────▷ │
    │                         │
    │◂┈┈┈┈┈loginSuccess┈┈┈┈┈┈ │
```

## Erweitetes Beispiel: Online-Bestellung

```
Kunde                       Onlineshop
  │                              │
  │──produktSuchen(kriterien)──▷ │
  │                              │
  │◂┈┈┈┈produktListe┈┈┈┈┈┈┈┈┈┈┈ │
  │                              │
  │──produktInWarenkorb(id)────▷ │
  │                              │
  │◂┈┈┈┈┈bestätigung┈┈┈┈┈┈┈┈┈┈┈ │
  │                              │
  │──checkout()────────────────▷ │
  │                              │
  │◂┈┈┈┈zahlungsformular┈┈┈┈┈┈┈ │
  │                              │
  │──bezahlen(daten)───────────▷ │
  │                              │
  │◂┈┈┈┈bestellbestätigung┈┈┈┈┈ │
```

## Mit Kontrollstrukturen

```
Benutzer                    ATM-System
    │                           │
    │──karteEinführen()────────▷ │
    │                           │
    │◂┈┈┈┈pinEingabeAufforderung │
    │                           │
    │ loop [versuche < 3]       │
    │   ──pinEingeben(pin)────▷ │
    │                           │
    │   alt [pin korrekt]       │
    │     ◂┈┈menüAnzeigen┈┈┈┈┈┈ │
    │   else                    │
    │     ◂┈┈fehlerMeldung┈┈┈┈┈ │
    │   end                     │
    │ end                       │
```

## Häufige Fehler vermeiden

- **Keine internen Systemdetails** zeigen
- **Zeit fließt nach unten** - nicht durcheinander
- **Akteur startet immer** den Use Case
- **Klare Nachrichtennamen** verwenden
- **Nicht zu detailliert** werden - nur systemrelevante Nachrichten
- **Konsistente Pfeiltypen** verwenden
- **Lebenslinien nicht vergessen**

## Checkliste für ein gutes SSD

- [ ] Akteur und System klar identifiziert
- [ ] Lebenslinien vorhanden
- [ ] Chronologische Reihenfolge eingehalten
- [ ] Passende Pfeiltypen gewählt
- [ ] Nachrichtennamen sind aussagekräftig
- [ ] Bedingungen und Schleifen korrekt notiert
- [ ] Keine internen Systemdetails gezeigt
- [ ] Rückgaben wo sinnvoll eingetragen

## Fazit

Das SSD ist der erste Schritt zur Systemanalyse. Es zeigt **WAS** zwischen Akteur und System passiert, aber nicht **WIE** das System intern arbeitet. Es bildet die Grundlage für detailliertere Diagramme in der Systemgestaltung.