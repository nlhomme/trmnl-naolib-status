# Naolib Departures – TRMNL Plugin

Display real-time next departures from the nearest Naolib (TAN) stop at any location in Nantes.

## How it works

1. A Cloudflare Worker (`worker.js`) acts as a proxy: it fetches nearby stops and their live departure times from the TAN API, then returns the combined data in TRMNL's `merge_variables` format.
2. TRMNL polls the Worker URL with your coordinates and renders the departure board using `full.liquid`.
3. The display refreshes every minute.

## Architecture

```
TRMNL polls → Cloudflare Worker?lat=...&lng=...
                → TAN /arrets.json (nearby stops)
                → TAN /tempsattente.json (departures)
                → returns { merge_variables: { stop, departures, refreshed_at } }
TRMNL renders full.liquid with {{ merge_variables.* }}
```

## Setup

### 1. Deploy the Cloudflare Worker

You need a [Cloudflare account](https://dash.cloudflare.com/sign-up) (free tier works).

```bash
npm install -g wrangler
wrangler login
wrangler deploy
```

The Worker URL will be printed after deploy (e.g. `https://naolib-worker.your-subdomain.workers.dev`).

### 2. Find your coordinates

Use any map tool (e.g. Google Maps → right-click → copy coordinates) to get the latitude and longitude of the location you want to monitor.

The TAN API uses a **comma** as the decimal separator (French locale). Replace dots with commas:

| Standard format | TAN API format |
|---|---|
| `47.21661` | `47,21661` |
| `-1.556754` | `-1,556754` |

### 3. Create a TRMNL private plugin

In the [TRMNL dashboard](https://usetrmnl.com), create a new private plugin with:

- **Strategy**: Polling
- **Polling URL**: Your Worker URL with coordinates, e.g.:
  ```
  https://naolib-worker.your-subdomain.workers.dev/?lat=47,21661&lng=-1,556754
  ```
- **Refresh interval**: 60 seconds

### 4. Paste the template

Copy the contents of `full.liquid` into the **Full** markup field in the TRMNL plugin editor.

## Display

- **Rounded-square badge** = tram line
- **Pill badge** = bus line
- **Filled dot** = real-time data
- **Hollow dot** = scheduled time only
- Wait times shown in minutes (e.g. `5mn`, `proche`)

## Files

| File | Description |
|---|---|
| `worker.js` | Cloudflare Worker — fetches stops + departures from TAN API |
| `wrangler.toml` | Worker deployment config |
| `full.liquid` | TRMNL Liquid template using the [Framework design system](https://trmnl.com/framework) |
| `settings.yml` | Plugin settings reference (not synced to TRMNL) |

## API

This plugin uses the [TAN open data API](https://open.tan.fr/ewp/). No API key required.

- Stops near coordinates: `https://open.tan.fr/ewp/arrets.json/{lat}/{lng}`
- Next departures: `https://open.tan.fr/ewp/tempsattente.json/{codeLieu}`
