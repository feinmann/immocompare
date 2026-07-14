# Spezifikation: immocompare

**Version:** 0.1 (Entwurf) · **Stand:** 14.07.2026 · **Status:** In Abstimmung

---

## 1. Vision & Zielsetzung

immocompare ist eine Webanwendung, mit der Nutzer eine oder mehrere Adressen (z. B. Immobilien-Kandidaten) hinsichtlich ihrer Lagequalität vergleichen können. Der Nutzer definiert frei benennbare Kategorien von "Points of Interest" (POIs) – etwa Einkaufsmöglichkeiten, Bildungseinrichtungen, Ärzte, Apotheken oder Spielplätze. Die Anwendung ermittelt die relevanten POIs im Umfeld der Adressen, visualisiert sie auf einer Karte und stellt die Entfernungen (Luftlinie sowie Fußweg/Fahrzeit) grafisch und tabellarisch gegenüber.

**Kernnutzen:** Objektive, schnelle Vergleichbarkeit der Lagequalität mehrerer Adressen auf einen Blick – ohne Registrierung, ohne Kosten für Kartendaten.

## 2. Zielgruppe & Anwendungsfälle

- **Immobiliensuchende (Kauf/Miete):** Vergleich von 2–5 Objektkandidaten hinsichtlich Alltagstauglichkeit der Lage.
- **Familien:** Fokus auf Schulen, Kitas, Spielplätze, Kinderärzte.
- **Senioren / Angehörige:** Fokus auf Ärzte, Apotheken, Nahversorgung, ÖPNV.
- **Makler:** Aufbereitung der Lagequalität eines Objekts als teilbarer Link für Interessenten.

## 3. Funktionale Anforderungen

### 3.1 Adressverwaltung (FA-1)

- FA-1.1: Der Nutzer kann eine oder mehrere Adressen eingeben (Freitext mit Autovervollständigung via Geocoding).
- FA-1.2: Jede Adresse erhält einen frei editierbaren Anzeigenamen (z. B. "Wohnung Gartenstraße") und eine eindeutige Farbe/Markierung.
- FA-1.3: Adressen können bearbeitet, entfernt und (MVP: bis zu 5, technisch konfigurierbar) hinzugefügt werden.
- FA-1.4: Bei nicht eindeutiger Eingabe werden Geocoding-Treffer zur Auswahl angeboten.
- FA-1.5: Alternativ kann eine Adresse per Klick auf die Karte gesetzt werden (Reverse Geocoding liefert die Anschrift).

### 3.2 Kategorienverwaltung (FA-2)

- FA-2.1: Der Nutzer kann beliebig viele Kategorien anlegen, umbenennen, deaktivieren und löschen.
- FA-2.2: Vordefinierte Kategorien als Startvorschlag: Supermarkt/Einkauf, Bäckerei, Apotheke, Hausarzt, Kinderarzt, Krankenhaus, Kita, Grundschule, Weiterführende Schule, Spielplatz, Park/Grünfläche, ÖPNV-Haltestelle, Bahnhof, Tankstelle, Restaurant/Café, Sportstätte/Fitness.
- FA-2.3: Jede Kategorie wird intern auf einen oder mehrere OSM-Tags gemappt (z. B. "Apotheke" → `amenity=pharmacy`). Für eigene Kategorien wählt der Nutzer aus einer kuratierten, verständlich benannten Tag-Liste (kein OSM-Wissen erforderlich); Experten können Tags direkt eingeben (Ausbaustufe).
- FA-2.4: Pro Kategorie sind konfigurierbar: Suchradius (Standard 2 km, max. 10 km), max. Anzahl anzuzeigender Treffer pro Adresse (Standard: 3 nächstgelegene), Icon und Farbe.
- FA-2.5: Kategorien lassen sich einzeln ein-/ausblenden (Karte und Tabelle).

### 3.3 POI-Suche & Entfernungsberechnung (FA-3)

- FA-3.1: Für jede aktive Kategorie werden je Adresse die nächstgelegenen POIs im Suchradius ermittelt (Overpass API).
- FA-3.2: Zu jedem POI werden berechnet:
  - Luftlinie (Haversine, clientseitig, sofort verfügbar),
  - Fußweg: Distanz in m/km und Gehzeit in Minuten (Routing-Engine),
  - Fahrzeit mit dem Auto: Distanz und Zeit (Routing-Engine).
- FA-3.3: Fahrrad als weiterer Routing-Modus ist als Ausbaustufe vorgesehen.
- FA-3.4: Routing-Ergebnisse werden asynchron nachgeladen; Luftlinien-Werte erscheinen sofort als Platzhalter und werden ersetzt, sobald Routing-Daten vorliegen.
- FA-3.5: Pro Kategorie und Adresse wird der jeweils nächstgelegene POI (nach Fußweg, Fallback Luftlinie) als "Bestwert" markiert.

### 3.4 Kartenvisualisierung (FA-4)

- FA-4.1: Interaktive Karte (Zoom, Pan) auf Basis von OpenStreetMap-Kacheln.
- FA-4.2: Adressen werden als prominente, farbcodierte Marker dargestellt; POIs als Kategorie-Icons in der Kategoriefarbe.
- FA-4.3: Entfernungsvisualisierung: Verbindungslinien von jeder Adresse zu ihren nächstgelegenen POIs, beschriftet mit Distanz/Zeit; optional Anzeige der tatsächlichen Fußweg-Route (Ausbaustufe).
- FA-4.4: Optionale Umkreis-Ringe (z. B. 500 m / 1 km / 2 km) um jede Adresse.
- FA-4.5: Klick auf einen POI öffnet ein Popup mit Name, Kategorie, Anschrift (falls vorhanden) und den Entfernungen zu allen eingegebenen Adressen.
- FA-4.6: Filterung der Kartenansicht nach Adresse und Kategorie; automatischer Kartenausschnitt, der alle aktiven Elemente umfasst.

### 3.5 Tabellarische Auswertung (FA-5)

- FA-5.1: Vergleichstabelle mit Kategorien als Zeilen und Adressen als Spalten; Zellen zeigen den nächstgelegenen POI mit Name, Fußweg (min/km), Fahrzeit und Luftlinie.
- FA-5.2: Umschaltbare Detailansicht: pro Kategorie die Top-N-POIs je Adresse.
- FA-5.3: Sortierung und Hervorhebung: Bestwert je Zeile wird visuell markiert (z. B. grün); optionale Ampelfärbung nach konfigurierbaren Schwellwerten (z. B. Fußweg < 5 min grün, < 15 min gelb, sonst rot).
- FA-5.4: Zusammenfassungszeile: einfacher Lage-Score je Adresse (Durchschnittsrang über alle aktiven Kategorien, MVP-Definition; gewichtbare Kategorien als Ausbaustufe).
- FA-5.5: Export der Tabelle als CSV; Export des Gesamtvergleichs als PDF (Ausbaustufe).

### 3.6 Teilen & Persistenz (FA-6)

- FA-6.1: Keine Nutzerkonten. Der aktuelle Vergleich (Adressen, Kategorien, Einstellungen) wird lokal im Browser gehalten und übersteht ein Neuladen der Seite (Local Storage).
- FA-6.2: "Teilen"-Funktion erzeugt einen Link, der den vollständigen Vergleichszustand kodiert. Bevorzugte Lösung: Zustand komprimiert in der URL (Fragment/Query, z. B. lz-string); dadurch serverlos teilbar. Fallback bei zu großem Zustand: serverseitig gespeicherter Snapshot mit Kurz-ID und definierter Aufbewahrungsdauer (z. B. 90 Tage).
- FA-6.3: Geöffnete geteilte Links sind vollwertig editierbar (Änderungen erzeugen einen neuen Link, der Original-Link bleibt unverändert).

## 4. Nicht-funktionale Anforderungen

- **NFA-1 Performance:** Erste Kartendarstellung < 2 s; POI-Ergebnisse einer Kategorie < 5 s nach Suche; Routing-Werte progressiv nachgeladen.
- **NFA-2 Responsivität:** Voll nutzbar auf Desktop, Tablet und Smartphone; auf Mobilgeräten umschaltbare Ansicht Karte/Tabelle.
- **NFA-3 Datenschutz:** Keine Registrierung, keine Speicherung personenbezogener Daten über den geteilten Zustand hinaus; DSGVO-konformes Hosting (EU); keine Third-Party-Tracker. Hinweis in der Datenschutzerklärung auf externe Dienste (Tiles, Geocoding, Routing).
- **NFA-4 Externe Dienste & Fair Use:** Einhaltung der Nutzungsbedingungen von Nominatim/Overpass (Rate Limits, User-Agent, Caching); mittelfristig eigene gehostete Instanzen oder kommerzielle OSM-basierte Anbieter, um Limits und Verfügbarkeit zu kontrollieren.
- **NFA-5 Barrierefreiheit:** Tabelle als vollwertige Alternative zur Karte; Tastaturbedienung; ausreichende Kontraste; Farbcodierung stets mit zweitem Merkmal (Icon/Text).
- **NFA-6 Internationalisierung:** UI zunächst Deutsch; Architektur i18n-fähig (Englisch als zweite Sprache in Ausbaustufe). Datenabdeckung weltweit, Fokus/Qualitätssicherung DACH.
- **NFA-7 Browser:** Aktuelle Versionen von Chrome, Firefox, Safari, Edge.

## 5. Technische Architektur

### 5.1 Überblick

Single-Page-Application mit schlankem Backend-Proxy. Das Backend kapselt externe Dienste (einheitliche API, Caching, Rate-Limit-Schutz, API-Schlüssel bleiben serverseitig).

```
[Browser: SPA + Leaflet/MapLibre]
        │ REST/JSON
[Backend-Proxy: Geocoding / POI / Routing / Share-Snapshots]
        │
[Nominatim] [Overpass API] [OSRM oder Valhalla] [Tile-Server]
        │
[Cache: Redis] [DB für Share-Snapshots: PostgreSQL oder SQLite]
```

### 5.2 Komponenten & Dienste (Vorschlag)

| Baustein | Vorschlag | Anmerkung |
|---|---|---|
| Frontend | React oder Vue, TypeScript | SPA, State via URL/Local Storage |
| Karte | Leaflet oder MapLibre GL | OSM-Raster- bzw. Vektor-Tiles |
| Tiles | OSM-Tiles (Start), später kommerzieller OSM-Anbieter | OSM-Tile-Policy beachten |
| Geocoding | Nominatim | Autocomplete gedrosselt (Debounce), Cache |
| POI-Suche | Overpass API | Abfragen je Kategorie/Radius, Cache nach Geohash |
| Routing | OSRM (Profile: foot, car) oder Valhalla | Matrix-/Table-API für viele POI-Paare |
| Backend | Node.js oder Python (FastAPI) | dünner Proxy + Caching |
| Cache | Redis | TTL z. B. 24 h für POI/Routing |
| Persistenz | PostgreSQL/SQLite | nur für Share-Snapshots (Fallback) |

### 5.3 API-Skizze (Backend)

- `GET /api/geocode?q=` → Geocoding-Treffer (Adresse, Koordinaten)
- `GET /api/reverse?lat=&lon=` → Anschrift zu Koordinate
- `POST /api/pois` → Body: Koordinaten, Kategorien (Tag-Mapping), Radius, Limit → POI-Liste
- `POST /api/routes/matrix` → Body: Quellen (Adressen), Ziele (POIs), Modus (foot/car) → Distanz-/Zeitmatrix
- `POST /api/share` / `GET /api/share/{id}` → Snapshot speichern/laden (nur Fallback)

### 5.4 Datenmodell (Client-Zustand)

- **Comparison**: id, Titel, createdAt, Einstellungen (Einheiten, Schwellwerte)
- **Address**: id, Label, Anschrift, lat/lon, Farbe
- **Category**: id, Name, OSM-Tag-Mapping[], Radius, Limit, Icon, Farbe, aktiv
- **PoiResult** (abgeleitet/gecacht): poiId, Name, Kategorie, lat/lon, je Adresse {Luftlinie, Fußweg{m,min}, Auto{m,min}}

## 6. UI/UX-Konzept (Grobentwurf)

- **Layout Desktop:** Linke Seitenleiste (Adressen, Kategorien, Einstellungen), Hauptbereich mit Tabs bzw. geteilter Ansicht Karte/Tabelle. Mobile: Bottom-Navigation Karte ↔ Tabelle ↔ Einstellungen.
- **Onboarding-Flow:** (1) Erste Adresse eingeben → (2) Kategorien aus Vorschlagsliste anhaken → (3) Ergebnis erscheint; weitere Adressen jederzeit ergänzbar. Leerer Zustand mit Beispiel-Demo ("Beispielvergleich laden").
- **Ladezustände:** Skeletons in der Tabelle; Luftlinie sofort, Routing-Werte mit Spinner-Ersatz.
- **Fehlerzustände:** Verständliche Meldungen bei Geocoding ohne Treffer, Overpass-Timeout (mit Retry) und Kategorien ohne POIs im Radius ("Kein Treffer im Umkreis von 2 km – Radius erhöhen?").

## 7. Roadmap

**MVP (Ausbaustufe 1)**
Adresseingabe (max. 5) mit Autocomplete · vordefinierte + eigene Kategorien (kuratierte Tag-Liste) · POI-Suche · Luftlinie + Fußweg + Auto · Karte mit Markern, Linien, Popups · Vergleichstabelle mit Bestwert-Markierung · Teilen per URL · Local-Storage-Persistenz · CSV-Export.

**Ausbaustufe 2**
Routen-Anzeige auf der Karte · Umkreis-Ringe · Fahrrad-Routing · gewichteter Lage-Score · Ampel-Schwellwerte konfigurierbar · PDF-Export · Englisch.

**Ausbaustufe 3**
ÖPNV-Reisezeiten (GTFS/Anbieter) · Isochronen (Erreichbarkeitsflächen) · eigene POIs manuell hinzufügen (z. B. "Arbeitsplatz", "Oma") · Snapshot-Server für sehr große Vergleiche.

## 8. Risiken & offene Punkte

- **R-1 Rate Limits öffentlicher OSM-Dienste:** Öffentliche Nominatim/Overpass-Instanzen sind für Produktivlast ungeeignet. Mitigation: aggressives Caching im MVP, früh Budget/Entscheidung für Self-Hosting oder kommerziellen OSM-Anbieter einplanen.
- **R-2 OSM-Datenqualität:** POI-Abdeckung variiert regional (v. a. Ärzte). Mitigation: Hinweis im UI ("Daten: OpenStreetMap, ohne Gewähr auf Vollständigkeit"), Feedback-Link zu OSM.
- **R-3 URL-Längenlimit beim Teilen:** Große Vergleiche sprengen ggf. die URL. Mitigation: Kompression; Snapshot-Fallback (FA-6.2).
- **R-4 Routing-Last:** n Adressen × m POIs erzeugt viele Matrixanfragen. Mitigation: Table-API nutzen, Limit Top-N pro Kategorie, Caching.
- **Offen:** Maximalzahl Adressen final festlegen · Definition des Lage-Scores validieren · Hosting/Betreiber & Impressum · Namens-/Markenprüfung "immocompare" · Monetarisierung (falls relevant) außerhalb des Scopes dieser Version.

## 9. Abnahmekriterien (Auszug, MVP)

1. Zwei Adressen in einer deutschen Großstadt eingegeben, Kategorien "Supermarkt" und "Apotheke" aktiviert → innerhalb von 10 s zeigen Karte und Tabelle je Adresse die 3 nächstgelegenen Treffer mit Luftlinie, Fußweg und Fahrzeit.
2. Der Bestwert je Kategorie ist in der Tabelle eindeutig markiert.
3. Der geteilte Link stellt den Vergleich in einem anderen Browser identisch wieder her.
4. Nach Neuladen der Seite ist der Vergleich unverändert vorhanden.
5. Auf einem Smartphone (390 px Breite) sind Karte und Tabelle vollständig bedienbar.
