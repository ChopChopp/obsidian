# Aktualisiertes Entity-Relationship Diagramm (ERD)

## Entities and Attributes

### `Firma`
- **FirmaID** (Primary Key)
- Name
- Adresse
- PLZ
- Ort
- Land

### `Produkt`
- **ProduktID** (Primary Key)
- Listenpreis

### `Typ`
- **TypID** (Primary Key)
- Bezeichnung (z.B. Laptop, Supercomputer)
- Zertifizierungsanforderung (Ja/Nein)
- Zertifizierungsintervall (in Jahren, z.B. 3 für Supercomputer)

### `Lager`
- **LagerID** (Primary Key)
- Standort (Adresse, PLZ, Ort, Land)

### `Lagerbestand`
- **LagerbestandID** (Primary Key)
- ProduktID (Foreign Key)
- LagerID (Foreign Key)
- Menge
- Einlagerungsdatum
- Auslagerungsdatum

### `Kunde`
- **KundeID** (Primary Key)
- Name
- Adresse
- PLZ
- Ort
- Land
- Typ (Privatkunde, Firmenkunde)

### `Werbung`
- **WerbungID** (Primary Key)
- Preis
- Datum

### `Lizenz`
- **LizenzID** (Primary Key)
- Lizenztyp

### `Kauf`
- **KaufID** (Primary Key)
- Kaufdatum

## Relationships

### `Firma` - `Produkt`
- Eine `Firma` bietet mehrere `Produkte` an.
- Beziehung: `produziert`

### `Produkt` - `Typ`
- Ein `Produkt` gehört zu einem `Typ`.
- Beziehung: `gehört_zu`

### `Produkt` - `Lager`
- Ein `Produkt` kann in mehreren `Lagern` gelagert werden.
- Beziehung: `gelagert_in`

### `Lagerbestand` - `Lager` - `Produkt`
- Der `Lagerbestand` verknüpft `Produkte` und `Lager` mit Mengenangaben und Daten.
- Beziehung: `Lagerbestand`

### `Produkt` - `Werbung`
- Ein `Produkt` kann in mehreren `Werbungen` beworben werden.
- Beziehung: `beworben_in`

### `Kunde` - `Produkt`
- Ein `Kunde` kann mehrere `Produkte` kaufen, wenn sie auf Lager sind.
- `Privatkunden` können nur `Laptops` kaufen.
- `Firmenkunden` können sowohl `Laptops` als auch `Supercomputer` kaufen.
- Beziehung: `kauft`

### `Kunde` - `Lizenz`
- `Firmenkunden` verwalten unterschiedliche `Lizenzen`.
- Beziehung: `verwaltet`

### `Kunde` - `Werbung`
- `Kunden` erhalten `Werbung` über `Produkte` von `Firmen`.
- Beziehung: `empfängt`

## Full Diagram Summary

- `Firma` **(FirmaID, Name, Adresse, PLZ, Ort, Land)**  
  ⬇️ `produziert`  
- `Produkt` **(ProduktID, Listenpreis)**  
  ⬇️ `gehört_zu`  
- `Typ` **(TypID, Bezeichnung, Zertifizierungsanforderung, Zertifizierungsintervall)**  
  ⬇️ `gelagert_in`  
- `Lager` **(LagerID, Standort)**  
  ⬇️ `Lagerbestand`  
- `Lagerbestand` **(LagerbestandID, ProduktID, LagerID, Menge, Einlagerungsdatum, Auslagerungsdatum)**  
  ⬇️ `beworben_in`  
- `Werbung` **(WerbungID, Preis, Datum)**  
  ⬇️ `empfängt`  
- `Kunde` **(KundeID, Name, Adresse, PLZ, Ort, Land, Typ)**  
  ⬇️ `kauft`  
  ⬇️ `verwaltet`  
- `Lizenz` **(LizenzID, Lizenztyp)**  
- `Kauf` **(KaufID, Kaufdatum)**