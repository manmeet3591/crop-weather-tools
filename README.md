# Crop Weather Tools

Prototype weather decision tools for **Kentucky fruit** (grapes, peaches) and **Arizona field crops** (cotton, alfalfa), built at Western Kentucky University with UK Extension and University of Arizona collaborators.

**Live dashboard:** https://manmeet3591.github.io/crop-weather-tools/

Each tool runs a published crop model on observed station weather, then carries it forward with a 16-day forecast and a 60-day ensemble outlook. The outlook is the slot for the WKU AI sub-seasonal-to-seasonal (S2S) forecast.

| Tool | Region | Model and thresholds |
|---|---|---|
| Grape bud hardiness | KY | Ferguson et al. (2014) *Am. J. Enol. Vitic.* 65:59, Table 4 parameters; run per ensemble member |
| Peach chill and freeze | KY | Chill hours (32–45°F) or Utah units (Richardson et al. 1974); MSU/WSU critical bud temperatures |
| Frost dates | KY | 30-year first/last freeze climatology plus forecast |
| Spray windows | KY | Hourly wind, rain chance and temperature rules of thumb |
| Cotton heat units | AZ | 86/55°F HUAP; stage benchmarks and heat-stress levels as used by AZMet |
| Alfalfa water use | AZ | AZMet Penman-Monteith ETo × FAO-56 alfalfa Kc for individual cutting cycles |

## How it works

- `index.html` is the whole app: plain HTML, CSS and JavaScript, no build step.
- `data/data.txt` holds the weather it reads. A GitHub Action (`.github/workflows/update-data.yml`) runs `scripts/update_data.py` every morning and commits fresh data from:
  - [ACIS](https://www.rcc-acis.org/) daily observations for Kentucky NWS stations
  - the [AZMet API](https://azmet.arizona.edu/) for Arizona stations
  - [Open-Meteo](https://open-meteo.com/) forecast and seasonal ensemble APIs
- To run the update yourself: `pip install requests && python scripts/update_data.py`, then serve the folder (`python -m http.server`) and open `http://localhost:8000`.

## Plugging in the WKU S2S model

`scripts/update_data.py` writes the outlook as `S0`…`S20` lines (daily mean °F per ensemble member, 60 days) and `SMED` (median daily max/min, plus ET0 for Arizona). Replace the `seasonal()` call with the AI forecast output in the same shape and every tool picks it up.

## Status

Research prototype; not an official Extension product. Kentucky hybrid grapes (Chambourcin, Vidal Blanc, Traminette, Norton) are not yet parameterized in the hardiness model.
