# El Niño Rainfall Impact Forecast: Central Visayas 2027

> 🚧 **Work in progress.** Started October 2026; target completion November 30, 2026.

How much below-normal rainfall should each Central Visayas province expect in February–May 2027, given the strength of the current El Niño?

PAGASA declared El Niño conditions in June 2026, and models suggest the event will become very strong by late 2026 and last into early 2027. In the Philippines, the worst dry spells usually arrive in the dry season after the peak. This project uses historical rainfall and El Niño data to estimate how hard Cebu, Bohol, Negros Oriental, and Siquijor may be hit, then checks the forecast against what actually happens in 2027.

## Progress

The project is built in four parts, each with its own finished result.

- [ ] **Part 1: How strong is this El Niño?** Load the Oceanic Niño Index (ONI) and compare the 2026 event with past ones.
- [ ] **Part 2: What did past El Niños do to rainfall?** Compare rainfall in El Niño years with normal years, by province.
- [ ] **Part 3: Can we forecast it?** Build a baseline and a regression model, evaluated on held-out El Niño events.
- [ ] **Part 4: The 2027 forecast.** Forecast February–May 2027 rainfall anomalies with uncertainty ranges.
- [ ] **Follow-up (mid-2027):** Compare the forecast with observed rainfall.

## Data

All data sources are free and public.

| Dataset | Source | Used for |
| --- | --- | --- |
| Oceanic Niño Index (ONI) | NOAA Climate Prediction Center | El Niño strength |
| CHIRPS v2 monthly rainfall | UC Santa Barbara Climate Hazards Center | Rainfall by province |
| Province boundaries | PSA / HDX | Averaging rainfall per province |
| GSOD / GHCN-Daily station data | NOAA | Optional cross-check of CHIRPS |

Raw data is not stored in this repository. The pipeline downloads it into `data/`.

## Project structure

```
├── src/          # Pipeline and model code
├── tests/        # Unit tests
├── notebooks/    # Exploration and charts
├── data/         # Downloaded data (gitignored)
└── docs/
    ├── proposal.md    # Project proposal
    └── decisions.md   # Log of key decisions and why
```

## Getting started

Requires [uv](https://docs.astral.sh/uv/).

```bash
git clone <repo-url>
cd elnino-visayas-forecast
uv sync
uv run pytest
```

Pipeline commands will be added as each part is completed.

## Tools

Python, pandas, DuckDB, pytest, Ruff, uv.

## Documentation

- [Project proposal](docs/proposal.md): research question, approach, success criteria, and scope
- [Decision log](docs/decisions.md): what changed and why
