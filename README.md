# immocompare

Lagequalität von Adressen vergleichen: Adressen eingeben, Kategorien (Supermarkt, Apotheke, Schule, …) wählen – immocompare zeigt die nächstgelegenen Orte auf einer Karte und stellt die Entfernungen tabellarisch gegenüber.

## Testversion 0.3 – Umfang

- Bis zu 5 Adressen mit Geocoding (Geoapify Geocoding API) und farbcodierten Markern
- 12 vordefinierte Kategorien (inkl. Café, Restaurant, Bad/Badesee) (Geoapify Places API, Datenbasis OpenStreetMap)
- Nächstgelegene Treffer je Kategorie (Radius 2 km, Top 3)
- Karte (Leaflet) mit Verbindungslinien zum jeweils nächsten Treffer
- Vergleichstabelle mit Bestwert-Hervorhebung
- Entfernungen: Luftlinie (Haversine) sowie **Auto- und Radzeiten** zum jeweils nächstgelegenen Treffer (Geoapify Route Matrix API, progressiv nachgeladen)

Noch nicht enthalten (siehe [Spec](docs/spec.md)): Fußweg-Routing, eigene Kategorien, Teilen per Link, Local-Storage-Persistenz, CSV-Export.

## Starten

Kein Build nötig – reines HTML/JS:

```bash
# Variante 1: Datei direkt im Browser öffnen
open index.html

# Variante 2: lokaler Server (empfohlen)
python3 -m http.server 8000
# → http://localhost:8000
```

## Konfiguration

Geocoding und POI-Suche laufen über [Geoapify](https://www.geoapify.com/) (OSM-basiert, Free-Plan: 3.000 Credits/Tag). Der API-Key steht in `index.html` (`GEOAPIFY_KEY`). Eigenen Key unter myprojects.geoapify.com anlegen und dort ersetzen.

## Hinweise

- Der Key ist im Client sichtbar. Für den Prototyp akzeptabel; im Geoapify-Dashboard sollte er auf die eigene Domain eingeschränkt werden, für den Produktivbetrieb ist der Backend-Proxy aus der Spec vorgesehen.
- Nominatim/Overpass werden seit v0.2 nicht mehr genutzt (öffentliche Instanzen waren zu unzuverlässig, siehe Spec Risiko R-1).
- Daten © OpenStreetMap-Mitwirkende (ODbL), bereitgestellt via Geoapify. Vollständigkeit der POIs variiert regional.

## Roadmap (Kurzfassung)

- **v0.4:** Fußweg-Modus, Teilen per URL, Local Storage
- **v0.5:** eigene Kategorien, konfigurierbarer Radius, CSV-Export
- Details: [docs/spec.md](docs/spec.md)
