# Sunshine Atlas — open dataset

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.21322408.svg)](https://doi.org/10.5281/zenodo.21322408)

Monthly **0–100 Sunshine Scores**, day/night temperatures, rainfall and sea
temperature for **3,833 destinations worldwide** — every one of them served by
its own airport (one primary airport per metro area, so London is Heathrow and
Southend rather than all five fields; see *Nine metros appear twice* below).
Built from NASA POWER's 2001–2020 climate normals, not forecasts.

## Provenance

Every value is either a measurement from the sources listed at the bottom of
this file, or computed from them by a formula documented on the
[methodology page](https://sunshineatlas.com/methodology/). **Nothing in this
dataset is written, estimated or filled in by a language model.**

Three honest caveats, so you can judge fitness for your own use:

- **`annual_sunshine_hours` is modelled, not observed.** It comes from NASA
  POWER's all-sky clearness index via the Ångström–Prescott relation, not from
  a sunshine recorder. Validated against 410 published national met-service
  figures: **r = 0.92, mean absolute error 212 h/yr, bias +24 h/yr.** It reads
  high where persistent low marine cloud or dust sits under a satellite's view
  — Lima and Cape Verde are the clearest cases, both ~35–40% over — and low in
  the perpetually-overcast Sichuan basin. This is a known limitation of
  satellite-derived sunshine (Kothe et al. 2017, *Remote Sensing* 9(5), 429).
- **`sea_temp_c` is a single year (2024)**, not a normal like everything else.
- **`climate` and `destination_type` are rule-based classifications**, not
  source fields. `sunshine_score_*` is an opinionated index, not a measurement.

This is a mirror of the canonical dataset at
**[sunshineatlas.com/data](https://sunshineatlas.com/data/)**, which regenerates
with every site build. The interactive version is the
[Sunshine Atlas globe](https://sunshineatlas.com).

## Files

| File | Contents |
|---|---|
| `sunshine-atlas-destinations.csv` | One row per destination: IATA code, city, country, continent, coordinates, elevation, population, annual sunshine hours, annual rainfall (mm), peak midday UV index, climate band, destination type, best month, and the Sunshine Score for the year + all 12 months. |
| `sunshine-atlas-monthly-climate.csv` | One row per destination-month (~46,000 rows): Sunshine Score, average day high °C, night low °C, rainfall mm, midday UV index, and sea-surface temperature °C for coastal places. |
| `sunshine-atlas-destinations.json` | Everything above as one JSON array, monthly values as arrays. |

Join key across files: `iata` (the destination's primary airport).

### Nine metros appear twice

The rule is one primary airport per metro, but nine metros genuinely run two
airports far enough apart to sit in different climate cells, so they hold two
rows sharing a `city` value.

Since the 2026.11 edition you do not have to know which nine: the
`duplicate_of_iata` column (`duplicateOfIata` in the JSON) is empty on every
primary and carries the primary's code on each second row, so filtering it
empty gives exactly one row per place — 3,824 of the 3,833. Both rows are real;
each holds the climate of its own coordinates, which is why a pair rarely
agrees exactly. The nine pairs are:

`LHR`/`SEN` London · `GLA`/`PIK` Glasgow · `MEL`/`MEB` Melbourne ·
`SEA`/`PAE` Seattle · `YUL`/`YHU` Montréal · `KEF`/`RKV` Reykjavík ·
`MMY`/`SHI` Miyakojima · `ULV`/`ULY` Ulyanovsk · `BZI`/`EDO` Balıkesir

Cities that merely share a **name** are not duplicates and carry a qualifier —
`Portland` (Oregon) vs `Portland (Maine)`, `Victoria (Seychelles)` vs
`Victoria (British Columbia)`. They are distinct places with distinct `iata`.

## Quick start

```python
import pandas as pd

monthly = pd.read_csv("sunshine-atlas-monthly-climate.csv")

# The sunniest places in November
nov = monthly[monthly.month == "Nov"].nlargest(10, "sunshine_score")
print(nov[["city", "sunshine_score", "day_high_c", "sea_temp_c"]])
```

Every destination has a deep-linkable page
(`https://sunshineatlas.com/destinations/<city>-<iata>/`, the `url` field in
the JSON) — and the dataset is queryable live by AI assistants over
[MCP](https://sunshineatlas.com/mcp/): endpoint
`https://sunshineatlas.com/api/mcp`, read-only, no key.

## The Sunshine Score

One number, 0–100, for how good a destination's sunshine is in a given month:
**daytime warmth** (full marks 20–32 °C) × **dryness** (monthly precipitation)
× **sunniness** (annual sunshine hours). A freezing-but-clear month scores
zero on purpose — it measures *pleasant* sunshine. Full formula:
[sunshineatlas.com/methodology](https://sunshineatlas.com/methodology/).

## License

[CC BY 4.0](https://creativecommons.org/licenses/by/4.0/) — free for research,
journalism and apps. Credit **Sunshine Atlas** and link to
<https://sunshineatlas.com/data/>.

> Sunshine Atlas (2026). *Monthly sunshine scores and climate normals for
> 3,833 destinations.* sunshineatlas.com/data/

Upstream sources: temperature, rainfall and sunshine from
[NASA POWER](https://power.larc.nasa.gov/) climatology, January 2001 –
December 2020 (CC BY 4.0); places & populations ©
[GeoNames](https://www.geonames.org/) & OurAirports (CC BY), with manual
name/population corrections where an airport's host municipality differs from
the destination it serves (island, region and metro labels use commonly-quoted
census figures); elevations from
the Copernicus DEM via [Open-Meteo](https://open-meteo.com/); sea temperatures
via Open-Meteo Marine (CC BY 4.0). Every upstream source permits commercial
use and redistribution, so this CC BY 4.0 grant carries no hidden conditions —
pass those credits along and you're done.

## Versioning

This mirror is the **2026.11 edition**. It adds one column,
**`duplicate_of_iata`** (`duplicateOfIata` in the JSON), described under *Nine
metros appear twice* above: the nine second airports now name their primary in
the data itself instead of only in this README. It is additive — no existing
value, column order or page `url` changed, and the climate values are identical
to 2026.10.

It also **corrects the published accuracy figures**. Earlier editions said the
modelled sunshine hours had been checked against 404 met-service cities with a
bias of +22 h/yr; re-running the validation against the current data gives
**410 cities** and a bias of **+24 h/yr**. Correlation (0.92) and mean absolute
error (212 h/yr) are unchanged. The old figures were correct when written and
drifted as destination names were corrected in 2026.09 and 2026.10, which let
more cities match the reference list.

The **2026.10 edition** added a **midday UV index** — peak
value per destination in `sunshine-atlas-destinations.csv`, and per
destination-month in `sunshine-atlas-monthly-climate.csv` and the JSON — on the
WHO scale, derived from the same NASA POWER climatology as everything else.
It also **disambiguates 22 destinations that shared a name with a different
place**: `Portland (Maine)` is now distinct from Portland, Oregon, and likewise
Victoria (Seychelles)/(British Columbia), London (Ontario), Birmingham
(Alabama), Manchester (New Hampshire), St. Petersburg (Florida), Kochi (Japan),
Jackson Hole, Hamilton Island and the rest. Two labels were also corrected to
the place the airport actually serves: `RUN` Sainte-Marie → Saint-Denis
(Réunion), `SMS` Sainte-Marie → Île Sainte-Marie. No `iata` changed, so joins
and page `url`s are unaffected, and climate values are identical to 2026.09.

The 2026.09 edition corrected **118 destination names**
where the record carried the airport's host municipality instead of the
destination it serves (Gaziemir → Izmir, Årø → Molde, Ciudad de la Costa →
Montevideo, Beringin → Medan…) and **146 population figures** that described a
catchment or neighbouring city rather than the named place (Trenton no longer
shows New York's 19M). Page `url`s are unchanged. Climate values are identical
to 2026.08. Climate normals change rarely, so
mirrored editions refresh only when the underlying data does; the canonical
files at [sunshineatlas.com/data](https://sunshineatlas.com/data/) regenerate
with every site build and are always current. Spotted an oddity? Open an
issue — corrections flow back into the site and the next edition.

## Related

[TrainRouter](https://trainrouter.com) · [Castlemap](https://thecastlemap.com) ·
[Beachmap](https://europebeachmap.com) — sister atlases from the same maker.
