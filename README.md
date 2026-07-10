# Sunshine Atlas — open dataset

Monthly **0–100 Sunshine Scores**, day/night temperatures, rainfall and sea temperature for **3,833 destinations worldwide** — every one of them served by its own airport (one primary airport per metro area: London appears once, not five times). Built from long-term climate normals, not forecasts.

This mirrors the canonical dataset at **[sunshineatlas.com/data](https://sunshineatlas.com/data/)**, which regenerates with every site build — grab the latest there. The interactive version is the [Sunshine Atlas globe](https://sunshineatlas.com).

## Files

| File | Contents |
|---|---|
| sunshine-atlas-destinations.csv | One row per destination: IATA code, city, country, continent, coordinates, elevation, population, annual sunshine hours, rainy days, climate band, destination type, best month, and the Sunshine Score for the year + all 12 months. |
| sunshine-atlas-monthly-climate.csv | One row per destination-month (~46,000 rows): Sunshine Score, average day high °C, night low °C, rainfall mm, and sea-surface temperature °C for coastal places. |
| sunshine-atlas-destinations.json | Everything above as one JSON array, monthly values as arrays. |

Join key across files: **iata** (the destination's primary airport).

## The Sunshine Score

One number, 0–100, for how good a destination's sunshine is in a given month: **daytime warmth** (full marks 20–32 °C) × **dryness** (monthly precipitation) × **sunniness** (annual sunshine hours). A freezing-but-clear month scores zero on purpose — it measures *pleasant* sunshine. Full formula: [sunshineatlas.com/methodology](https://sunshineatlas.com/methodology/).

## License

[CC BY 4.0](https://creativecommons.org/licenses/by/4.0/) — free for research, journalism and apps. Credit **Sunshine Atlas** and link to [sunshineatlas.com/data](https://sunshineatlas.com/data/).

> Sunshine Atlas (2026). *Monthly sunshine scores and climate normals for 3,833 destinations.* sunshineatlas.com/data/

Upstream sources: climate normals from CRU climatology; places & populations © [GeoNames](https://www.geonames.org/) & OurAirports (CC BY); sea temperatures via [Open-Meteo](https://open-meteo.com/) (CC BY 4.0).

## Related

[TrainRouter](https://trainrouter.com) · [Castlemap](https://thecastlemap.com) · [Beachmap](https://europebeachmap.com) — sister atlases from the same maker.
