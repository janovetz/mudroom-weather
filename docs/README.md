# Mudroom Weather — Documentation

Mudroom Weather is a single-page weather display for an always-on tablet. It also works on phones and desktop browsers. Everything is in `index.html`: no build step, no server-side code, no API keys.

- [Setup](#setup)
- [Screen layout](#screen-layout)
- [What to wear](#what-to-wear)
- [Heads-up line](#heads-up-line)
- [Spoken summary](#spoken-summary)
- [Pet Mode](#pet-mode)
- [Settings](#settings)
- [Refresh and offline behavior](#refresh-and-offline-behavior)
- [Data sources](#data-sources)
- [Development](#development)

---

## Setup

### Location

No location is built into the code. On load, the page picks a location in this order:

1. The `lat`, `lon` and optional `name` query parameters:
   ```
   https://janovetz.github.io/mudroom-weather/?lat=45.00&lon=-122.00&name=Home
   ```
2. The last location saved in the browser.
3. The device location (needs permission and HTTPS or `localhost`).
4. If none of those work, Settings opens so you can search for a town or ZIP code.

For US locations without a `name`, the nearest town is looked up from the National Weather Service.

### Tablet (kiosk) use

- Open the page from a bookmark or home-screen icon. It runs full screen when added to the home screen.
- The screen is kept awake with the Screen Wake Lock API (HTTPS only).
- The page reloads itself every 12 hours.
- The layout is tuned for 1440×960 landscape. Portrait screens and phones get a stacked, scrolling layout.
- Supported browsers: current Safari, Chrome, Edge and Firefox. Older devices (for example iOS 12) are not supported.

---

## Screen layout

| Area | Contents | Interaction |
|---|---|---|
| Header | Place name, update status, clock and date | ⚙ opens Settings |
| Alerts | Active NWS watches, warnings and advisories (US only). Warnings and Severe/Extreme alerts are red; others are orange. | Tap to expand the full text |
| Conditions | Icon, temperature, sky, feels-like, today's high and low | 🔊 reads the [spoken summary](#spoken-summary) |
| What to wear | Six tiles plus a one-line reason. See [What to wear](#what-to-wear). | Tap a tile for its reasoning. **Out for N hr** sets the time out. |
| Heads-up | Notable changes in the next 18 hours. See [Heads-up line](#heads-up-line). | — |
| Stats | Wind (with gusts when they exceed the sustained speed by 8+), humidity and dew point, UV and US AQI, pressure, next sunrise or sunset | Hover AQI for the PM2.5 value |
| NWS forecast | The next two NWS text forecast periods (US only) | — |
| Radar | Animated radar loop with the home location marked | Tap the map to toggle full screen (Esc exits). + / − zoom around home; ⌂ re-centers. ▶/❚❚ plays or pauses. |
| Forecast strip | Next 24 hours → 7 days → all available days (up to 16) | Tap to cycle views |

In the forecast strip, the shaded bar behind each column shows the chance of precipitation (from 10%); the percentage is printed from 20%.

AQI uses the EPA categories: Good (0–50), Moderate (51–100), Sensitive (101–150), Unhealthy (151–200), Very unhealthy (201–300), Hazardous (301+).

---

## What to wear

Recommendations cover the **time out**: from now until 1, 2 or 3 hours from now, chosen with **Out for N hr**. They update every minute. Tap any tile for the values and set points behind its pick.

### Terms

| Term | Meaning |
|---|---|
| Expected precipitation | Hours in the time out whose precipitation chance is at least the *Count as expected* set point (default 40%) |
| Peak rate | The highest of the current precipitation rate and the expected hourly amounts |
| Showers possible | Any hour in the time out with precipitation in the forecast and a chance at or above the *Soft Shell* chance set point |
| Coldest feels-like | The lowest apparent temperature from the hour before now through the end of the time out |
| Wet-feet chance | `1 − (1 − ground) × (1 − sky)`. *Ground* is the last 12 hours of precipitation relative to 0.10 in, with older rain counting less (it halves every *dries by half* hours). *Sky* is the highest hourly precipitation chance during the time out. |
| Snow on the ground | Forecast snow depth of at least 0.05 ft (about 0.6 in / 1.5 cm) at the start of the time out |
| Ground surface | The forecast model's surface (0 cm soil) temperature. This is a model estimate for natural ground, not pavement. |

### Rules

Rules are evaluated top to bottom; the first match wins.

| Tile | Choice | When |
|---|---|---|
| **Headwear** | Gore-Tex | Peak rate ≥ *Gore-Tex hat* set point, or anything heavier than light drizzle is forecast |
| | Insulated | Insulation is Heavy |
| | Cap | Insulation is Medium, UV ≥ *Cap* set point, the shell is Gore-Tex (light drizzle), or showers are possible |
| | None | Otherwise |
| **Shell** | Gore-Tex | Peak rate ≥ *Gore-Tex shell* set point, or precipitation is reported now |
| | Soft Shell | Showers are possible, or wind ≥ *Soft Shell wind* set point |
| | None | Otherwise |
| **Insulation** | Heavy / Medium / Light / None | Coldest feels-like is below the Heavy, Medium or Light set point; None when it's at or above Light. Heavy includes gloves. |
| **Shell (Pants)** | Gore-Tex | Peak rate ≥ *Shell pants* set point, or moderate or heavier rain, snow or storms are forecast |
| | None | Otherwise |
| **Footwear** | Yak Trax | Freezing rain or drizzle is forecast, or ground surface ≤ *Yak Trax* set point **and** there is snow on the ground, snow expected, or the ground is still at least 25% wet |
| | Gore-Tex | The shell is Gore-Tex, there is snow on the ground, or wet-feet chance ≥ *Gore-Tex footwear* set point |
| | Light | Otherwise |
| **Hi-Viz** | On | The start or end of the time out falls in low light: from *Hi-Viz* minutes before sunset until the same number of minutes after sunrise |

### Set points

Adjust these in Settings → *What to wear*. They are stored in °F, inches and mph and shown in your chosen units. **Reset to defaults** restores the values below.

| Group | Set point | Default | Range |
|---|---|---|---|
| Headwear | Gore-Tex when drizzle ≥ | 0.02 in/hr | 0.01–0.20 |
| | Cap when UV index ≥ | 5 | 1–11 |
| Shell | Gore-Tex when rain/snow ≥ | 0.01 in/hr | 0.01–0.10 |
| | Soft Shell when shower chance ≥ | 25% | 5–80% |
| | Soft Shell when wind ≥ | 15 mph | 5–40 |
| Insulation | Heavy / Medium / Light below | 32 / 48 / 60 °F | −10–80 °F |
| Shell pants | Shell pants when rain/snow ≥ | 0.10 in/hr | 0.02–0.50 |
| Footwear | Gore-Tex when wet-feet chance ≥ | 50% | 10–90% |
| | Ground dries by half every | 4 hr | 1–12 |
| | Yak Trax when ground surface ≤ | 32 °F | 26–38 °F |
| Visibility | Hi-Viz from before sunset / after sunrise | 15 min | 0–60 |
| Pet Mode | Warn when air temperature ≥ | 77 °F | 65–95 °F |
| | …or ground surface ≥ | 115 °F | 90–140 °F |
| Forecast | Count rain/snow as expected at chance ≥ | 40% | 10–90% |

---

## Heads-up line

A short summary of the next 18 hours. Each item appears only when it applies:

| Item | Condition |
|---|---|
| Hot Paws Warning | [Pet Mode](#pet-mode) is on and it's hot during the time out |
| *Rain/snow* likely around *time* | Dry now; an hour with ≥ 50% chance and precipitation in the forecast is coming |
| Precipitation should end around *time* | Wet now; a dry hour is coming |
| Wet through at least *time* | Wet now and for the whole 18 hours |
| *N* in of snow possible | Total snowfall ≥ 0.2 in (0.5 cm) |
| Below freezing by *time* | Above freezing now, at or below freezing later |
| Warming to *N*° | Rises ≥ 12 °F above the current temperature |
| Dropping to *N*° by *time* | Falls ≥ 15 °F below the current temperature |
| Gusts to *N* mph | Gusts ≥ 35 mph (55 km/h) within 12 hours |
| No umbrella needed today | None of the above precipitation items, and today's highest chance is under 25% |

---

## Spoken summary

The 🔊 button next to the temperature reads a short summary using the browser's built-in speech (no network service). Tap again to stop. The button is hidden if the browser has no speech support.

The summary contains, in order:

1. Temperature (with units), sky, feels-like when it differs by 3° or more, today's high and low
2. Names of active NWS alerts
3. The heads-up items, except the umbrella note
4. What to wear for the time out. Tiles set to None and Light footwear are skipped.

Example:

> 72 Fahrenheit and clear. High 75, low 50. Dropping to 57 by 6 AM. For 2 hours: cap and light insulation.

---

## Pet Mode

Turn on in Settings → *Display* → **Pet Mode**. When it's on, a red **Hot Paws Warning - Check pavement temperature!** leads the heads-up line and the spoken summary whenever, during the time out:

- the air temperature reaches the *air* set point (default 77 °F), **or**
- the model's ground-surface temperature reaches the *ground* set point (default 115 °F).

Pavement in direct sun can be much hotter than either value; the warning is a prompt to check, not a measurement.

---

## Settings

Open with ⚙. Changes apply immediately and are saved in the browser (`localStorage`, keys prefixed `wx.`), so each device keeps its own settings.

| Setting | Options |
|---|---|
| Location | Search by city or ZIP, or **Use my location** |
| Units | °F · mph · in, or °C · km/h · mm |
| Display | **Dim at night**: dims the screen from 1 hour after sunset until 30 minutes before sunrise. Any touch brightens it for 60 seconds. **Pet Mode**: see [Pet Mode](#pet-mode). |
| What to wear | Set points; see [Set points](#set-points) |

The bottom of the dialog shows the page size in CSS and device pixels, which helps when sizing for a specific tablet, and links to this documentation (it opens in a new tab).

### About

The About box shows the first time the page opens on a device and stays until it is dismissed (**Got it**, Esc, or a tap outside it). Open it again with **Settings → About**. It links to this documentation and the source, and shows the version: the date and time the page was built, taken from the `Last-Modified` time GitHub Pages sends for the deployed page.

---

## Refresh and offline behavior

| Data | Refresh |
|---|---|
| Forecast and current conditions | Every 10 minutes (every 60 seconds while failing) |
| NWS alerts | Every 5 minutes |
| NWS text forecast, air quality | Every 10 minutes |
| Radar | Every 5 minutes; only the newest frame is fetched |
| What to wear, heads-up, Hi-Viz, dimming | Every minute |
| Whole page | Reload every 12 hours |

On load, the page draws immediately from the last saved forecast, then refreshes in the background. The header shows **Updated N min ago**; after 20 minutes without a successful update it turns orange and reads **Offline**. Returning to the page after it's been hidden refreshes anything that's out of date.

---

## Data sources

All sources are free and need no API key.

| Data | Source |
|---|---|
| Forecast, current conditions, ground surface temperature | [Open-Meteo Forecast API](https://open-meteo.com/) |
| Air quality (US AQI, PM2.5) | [Open-Meteo Air Quality API](https://open-meteo.com/en/docs/air-quality-api) |
| Alerts, text forecast, town names (US) | [National Weather Service API](https://www.weather.gov/documentation/services-web-api) |
| Radar, contiguous US | [Iowa Environmental Mesonet](https://mesonet.agron.iastate.edu/) NEXRAD composite: 11 frames, 5 minutes apart |
| Radar, elsewhere | [RainViewer](https://www.rainviewer.com/api.html) |
| Location search | [Open-Meteo Geocoding API](https://open-meteo.com/en/docs/geocoding-api) |
| Basemap | [Esri](https://www.esri.com/) Dark Gray Canvas |
| Map library | [Leaflet](https://leafletjs.com/) 1.9.4 |

---

## Development

Serve the folder with any static server and open it in a browser:

```
python3 -m http.server 8765
```

Then open <http://localhost:8765/>. Device location and the screen wake lock need `localhost` or HTTPS.

The site is published by GitHub Pages from the `main` branch; every push to `main` goes live.

| Part of `index.html` | Purpose |
|---|---|
| `WEAR_DEFAULTS`, `WEAR_SLIDERS` | What-to-wear set points, defaults and slider ranges |
| `whatToWear()` | What-to-wear rules and tile reasons |
| `headsUp()`, `hotPaws()` | Heads-up line and Pet Mode check |
| `spokenSummary()` | Text for the spoken summary |
| `radar` | Map, radar frames and animation |
| `cacheKey()` | Version of the saved forecast; bump it when the requested API fields change |
