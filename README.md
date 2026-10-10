# Crop Weather Tools

Prototype weather decision tools for **Kentucky fruit** (grapes, peaches) and **Arizona field crops** (cotton, alfalfa), built at Western Kentucky University with UK Extension and University of Arizona collaborators.

**Live dashboard:** https://manmeet3591.github.io/crop-weather-tools/

Each tool runs a published crop model on observed station weather, then carries it forward with a 16-day forecast and a 45-day ECMWF IFS sub-seasonal (S2S) ensemble outlook.

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

## Forecast sources

- Days 1–15: Open-Meteo forecast API.
- Beyond day 15, out to day 45: the **ECMWF IFS extended-range (sub-seasonal) ensemble, EC46**, 51 members (control + 50), requested with `models=ecmwf_ec46` from the Open-Meteo seasonal API. ECMWF runs it daily. Data CC BY 4.0.

To swap in a different S2S source (for example the WKU AI model), replace `seasonal()` in `scripts/update_data.py` with output in the same shape: `S0`…`S50` lines of daily mean °F per member and `SMED` lines of the ensemble median (plus ET0 in inches for Arizona).

## Status

Research prototype; not an official Extension product. Kentucky hybrid grapes (Chambourcin, Vidal Blanc, Traminette, Norton) are not yet parameterized in the hardiness model.
