# Mudroom Weather

A fast, ad-free, single-page weather display built for an always-on tablet (tuned for 1440×960 landscape, works on phones).

- Current conditions, "heads-up" summary, and NWS text forecast
- Animated radar (Iowa State NEXRAD composite in the US, RainViewer elsewhere) — tap to go full screen, +/−/home buttons to zoom
- Forecast strip — tap to cycle 24 hours → 7 days → 16 days
- NWS watches/warnings for US locations
- Keeps the screen awake, dims at night, remembers its view, paints instantly from the last forecast

No build step, no API keys: everything is in `index.html`.

## Location

Nothing is hard-coded. Open the page with your coordinates and it remembers them:

```
https://janovetz.github.io/mudroom-weather/?lat=45.00&lon=-122.00&name=Home
```

Or use the ⚙ settings to search for a town or use the device's location.

## Run locally

```
python3 -m http.server 8765
```

Then open http://localhost:8765/.

## Data

[Open-Meteo](https://open-meteo.com/) · [National Weather Service](https://www.weather.gov/documentation/services-web-api) · [Iowa Environmental Mesonet](https://mesonet.agron.iastate.edu/) · [RainViewer](https://www.rainviewer.com/api.html) · [Esri](https://www.esri.com/) basemap · [Leaflet](https://leafletjs.com/)
