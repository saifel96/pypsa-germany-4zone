# FlexGridOptimizer

A **PyPSA-based electricity system optimization model** for Germany, featuring a 4-zone network with renewable energy expansion, battery and hydrogen storage, and CO₂ emission constraints.

---

## Overview

FlexGridOptimizer models Germany's electricity system across four regions (**Nord, West, Ost, Süd**) to find the least-cost investment and dispatch plan that meets electricity demand while respecting transmission and emission constraints.

The model answers: **What is the optimal mix of wind, solar, gas, batteries, and hydrogen storage — and at what system cost — for a given CO₂ limit?**

---

## Features

- 4-zone network model with inter-regional transmission lines
- Technologies: Wind, Solar PV, OCGT (open-cycle gas turbine), Battery Storage, Hydrogen Storage (underground)
- Hourly timeseries for 2025 (load, wind capacity factors, solar capacity factors)
- 2030 technology cost projections (from [PyPSA technology-data](https://github.com/PyPSA/technology-data/blob/master/outputs/costs_2030.csv))
- CO₂ emission constraints (configurable scenarios)
- Linear optimisation solved with [HiGHS](https://highs.dev/)

---

## Scenarios & Key Results

Two scenarios were compared:

| Scenario | CO₂ Limit | System Cost |
|---|---|---|
| **A** — Moderate decarbonization | ~9 Mt/a | **57.8 bn€/a** |
| **B** — Full decarbonization | 0 Mt/a | **92.0 bn€/a** |

**Key findings:**
- Full decarbonization costs **+34.2 bn€/a (+59%)** over a moderate scenario
- In Scenario B, OCGT is replaced by massive solar expansion (161 → 337 GW in Süd) and batteries (capex: 7.56 → 41.3 bn€)
- Battery cycling increases ~3× without gas backup
- Wind grows modestly (125 → 153 GW in Nord), while solar becomes the dominant energy source

---

## Project Structure

```
FlexGridOptimizer/
├── pypsa_model.ipynb          # Main optimization model
├── requirements.txt           # Python dependencies
├── germany_4zones.geojson     # Zone geometries for mapping
├── data/
│   ├── costs_2030.csv         # Technology cost assumptions
│   ├── timeseries_2025_germany.csv  # Load + renewable CF timeseries
│   ├── load_hourly.csv        # Hourly load data
│   ├── load_entsoe_data.csv   # Raw ENTSO-E load data
│   ├── pv_cf_4zones_aggregated.csv  # Solar capacity factors per zone
│   ├── wind_cf_4zones_aggregated.csv # Wind capacity factors per zone
│   └── germany_4zones.geojson
└── preprocessing/
    ├── building_timeseries.ipynb    # Build combined timeseries
    ├── download_weather_data.ipynb  # Download ERA5 weather data
    └── get_load_entsoe.ipynb        # Fetch load data from ENTSO-E API
```

---

## Installation

```bash
git clone https://github.com/saifel96/pypsa-germany-4zone.git
cd pypsa-germany-4zone
pip install -r requirements.txt
```

---

## Usage

1. **(Optional) Run preprocessing notebooks** in `preprocessing/` to regenerate timeseries data
2. **Open and run** `pypsa_model.ipynb` to build the network and solve
3. Adjust the CO₂ constraint in the `GlobalConstraint` cell to explore different scenarios:
   ```python
   constant = 80e6   # ~80 Mt CO₂ (Scenario A)
   # constant = 0    # Zero emissions (Scenario B)
   ```

---

## Data Sources

- **Cost data:** [PyPSA technology-data 2030](https://github.com/PyPSA/technology-data/blob/master/outputs/costs_2030.csv)
- **Load data:** [ENTSO-E Transparency Platform](https://github.com/EnergieID/entsoe-py?tab=readme-ov-file)
- **Weather data:** ERA5 reanalysis (downloaded via `preprocessing/download_weather_data.ipynb`)

---

## Dependencies

Key packages (see `requirements.txt` for full list):

| Package | Purpose |
|---|---|
| `pypsa` | Energy system modelling & optimization |
| `linopy` | Linear programming interface |
| `highspy` | HiGHS solver |
| `pandas` / `numpy` | Data handling |
| `geopandas` | Geospatial zone data |
| `plotly` / `matplotlib` | Visualization |
| `netCDF4` | Weather data (ERA5) |

---

## Scenario Results Summary

### Installed Capacity

| Technology | Scenario A (CO₂ ≤ 9 Mt) | Scenario B (Zero CO₂) |
|---|---|---|
| Wind Nord | 125 GW | 153 GW |
| Solar Süd | 161 GW | 337 GW |
| OCGT | ~35 GW | 0 GW |

### Generation Mix

| Technology | Scenario A | Scenario B |
|---|---|---|
| Wind | 862 TWh | 440 TWh |
| Solar | 860 TWh | 1499 TWh |
| Gas (OCGT) | 181 TWh | 0 TWh |

---

## License

MIT License — see [LICENSE](LICENSE) for details.


