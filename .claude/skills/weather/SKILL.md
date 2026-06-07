---
name: weather
description: >
  Fetch and display current local weather information. Use when the user asks
  about the weather, current temperature, forecast, conditions outside, or
  invokes /weather. Retrieves live data from wttr.in using curl.
---

Fetch weather via wttr.in and present it clearly to the user.

## How to fetch

The default location is **Santa Ana, El Salvador**. Run the Bash tool with:

```bash
curl -s "wttr.in/Santa+Ana,+El+Salvador?format=j1"
```

This returns JSON. Extract and present:
- **Location** — `nearest_area[0].areaName[0].value`, `country[0].value`
- **Condition** — `current_condition[0].weatherDesc[0].value`
- **Temperature** — `current_condition[0].temp_C` °C / `temp_F` °F
- **Feels like** — `current_condition[0].FeelsLikeC` °C / `FeelsLikeF` °F
- **Humidity** — `current_condition[0].humidity` %
- **Wind** — `current_condition[0].windspeedKmph` km/h, `winddir16Point` (plain string, e.g. `"E"`)
- **Visibility** — `current_condition[0].visibility` km
- **3-day forecast** — `weather[0..2]`: date, `maxtempC`/`mintempC`, and `hourly[4].weatherDesc[0].value` for midday conditions

## Presentation format

Present results as a compact summary — no JSON dump. Example:

```
Buenos Aires, Argentina — Partly Cloudy
Temp: 18°C (64°F)  Feels like: 16°C  Humidity: 72%
Wind: 24 km/h NW   Visibility: 10 km

Forecast:
  Mon Jun 08 — 22° / 14°C — Sunny
  Tue Jun 09 — 19° / 13°C — Light Rain
  Wed Jun 10 — 17° / 11°C — Overcast
```

## Error handling

If curl fails or returns no data, tell the user that wttr.in is unreachable and suggest they check their internet connection.

## Location override

If the user provides a city name (e.g., `/weather Paris` or "weather in Osaka"), fetch:

```bash
curl -s "wttr.in/Paris?format=j1"
```

URL-encode spaces as `+` (e.g., `New+York`).
