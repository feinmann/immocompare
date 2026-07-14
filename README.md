# immocompare

Lagequalität von Adressen vergleichen: Adressen eingeben, Kategorien (Supermarkt, Apotheke, Schule, …) wählen – immocompare zeigt die nächstgelegenen Orte auf einer Karte und stellt die Entfernungen tabellarisch gegenüber.

## Testversion 0.1 – Umfang

- Bis zu 5 Adressen mit Geocoding (Nominatim) und farbcodierten Markern
- 9 vordefinierte Kategorien (OpenStreetMap-Tags, Overpass API)
- Nächstgelegene Treffer je Kategorie (Radius 2 km, Top 3)
- Karte (Leaflet) mit Verbindungslinien zum jeweils nächsten Treffer
- Vergleichstabelle mit Bestwert-Hervorhebung
- Entfernungen: **nur Luftlinie** (Haversine)

Noch nicht enthalten (siehe [Spec](docs/spec.md)): Fußweg/Fahrzeit (Routing), eigene Kategorien, Teilen per Link, Local-Storage-Persistenz, CSV-Export.

## Starten

Kein Build nötig – reines HTML/JS:

```bash
# Variante 1: Datei direkt im Browser öffnen
open index.html

# Variante 2: lokaler Server (empfohlen)
python3 -m http.server 8000
# → http://localhost:8000
```

## Hinweise

- Die App nutzt die **öffentlichen** Instanzen von Nominatim und Overpass. Diese sind nur für Entwicklung/Tests geeignet (Rate Limits, Fair-Use-Policy). Für den Produktivbetrieb sind eigene Instanzen oder ein kommerzieller OSM-Anbieter vorgesehen (siehe Spec, Risiko R-1).
- Daten © OpenStreetMap-Mitwirkende (ODbL). Vollständigkeit der POIs variiert regional.

## Roadmap (Kurzfassung)

- **v0.2:** Fußweg-/Fahrzeit-Routing (OSRM), Teilen per URL, Local Storage
- **v0.3:** eigene Kategorien, konfigurierbarer Radius, CSV-Export
- Details: [docs/spec.md](docs/spec.md)
