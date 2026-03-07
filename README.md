# Naolib Departures – TRMNL Plugin

Display real-time next departures from the nearest Naolib (TAN) stop at any location in Nantes.

## How it works

1. TRMNL polls the TAN open API with your coordinates to find nearby stops.
2. The template's JavaScript fetches live departure times for the nearest stop.
3. The display refreshes every minute.

## Setup

### 1. Find your coordinates

Use any map tool (e.g. Google Maps → right-click → copy coordinates) to get the latitude and longitude of the location you want to monitor.

### 2. Set the Polling URL

In your TRMNL plugin settings, set the **Polling URL** to:

```
https://open.tan.fr/ewp/arrets.json/{lat}/{lng}
```

The TAN API uses a **comma** as the decimal separator (French locale). Replace dots with commas:

| Standard format | TAN API format |
|---|---|
| `47.21661` | `47,21661` |
| `-1.556754` | `-1,556754` |

Example URL for the city centre:
```
https://open.tan.fr/ewp/arrets.json/47,21661/-1,556754
```

### 3. Paste the template

Copy the contents of `full.liquid` into the TRMNL plugin editor, then replace:

```js
const STOPS_DATA = [...];
```

with:

```js
const STOPS_DATA = {{ data | json }};
```

### 4. Set refresh interval

60 seconds is recommended.

## Display

- **Rounded-square badge** = tram line
- **Pill badge** = bus line
- **Filled dot** = real-time data
- **Hollow dot** = scheduled time only
- Wait times shown in minutes (e.g. `5mn`)

## API

This plugin uses the [TAN open data API](https://open.tan.fr/ewp/). No API key required.

- Stops near coordinates: `https://open.tan.fr/ewp/arrets.json/{lat}/{lng}`
- Next departures: `https://open.tan.fr/ewp/tempsattente.json/{codeLieu}`
