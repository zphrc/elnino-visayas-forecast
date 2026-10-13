# El Niño Rainfall Impact Forecast: Central Visayas 2027 — Project Proposal

Oct 13, 2026

## Research question

How much below-normal rainfall should each Central Visayas province expect in February–May 2027, given the strength of the current El Niño?

Provinces covered: Cebu, Bohol, Negros Oriental, Siquijor. The answer is a monthly rainfall anomaly forecast per province, with an uncertainty range.

## Background and why it matters

PAGASA declared El Niño conditions in June 2026 and expects the event to last into early 2027 ([advisory](https://pubfiles.pagasa.dost.gov.ph/pagasaweb/files/piias/press_release/6a27d9c5a503a_PressStatement_EN.pdf)). Its July advisory said models point to a very strong El Niño by October–December 2026 ([ENSO Advisory No. 2](https://pubfiles.pagasa.dost.gov.ph/pagasaweb/files/climate/monthlyclimateassessment/ENSO%20Advisory2%20OS.pdf)).

In the Philippines, the worst dry spells usually arrive in the dry season after the peak, so February–May 2027 is the window that matters.

Who could use the answer:

- Farmers and provincial agriculture offices planning planting and irrigation
- Water utilities and LGUs planning for supply shortfalls
- Disaster risk offices deciding where to pre-position drought support

## Data sources

All v1 sources are free and public; coverage dates are approximate until checked in week 1.

| Dataset | Provider | What it gives | Coverage | Role |
| --- | --- | --- | --- | --- |
| Oceanic Niño Index (ONI) | NOAA Climate Prediction Center | Monthly El Niño strength (°C anomaly) | 1950–present | Main predictor |
| CHIRPS v2 rainfall | UC Santa Barbara Climate Hazards Center | Monthly gridded rainfall (mm), \~5 km cells | 1981–present | Target variable |
| ENSO outlooks | PAGASA, NOAA/IRI | Forecast ONI for coming seasons | Current | Inputs for the 2027 forecast |
| Province boundaries | PSA / HDX admin boundaries | Shapefiles to average rainfall per province | Current | Aggregation |
| Station rainfall (optional) | NOAA GSOD / GHCN-Daily | Daily rainfall at Mactan and other stations | Varies | Cross-check CHIRPS |

Open question: which Central Visayas stations have usable GSOD/GHCN coverage since 1981? Check gaps before relying on them. (PAGASA station data was dropped because it requires a fee.)

## Approach

Model monthly rainfall anomaly per province from lagged ONI and season, and compare it against a simple baseline.

1. **Ingest.** Scheduled pipeline pulls ONI and CHIRPS monthly, clips rainfall to province boundaries, and stores it in DuckDB.
2. **Build the target.** Rainfall anomaly = monthly rainfall minus that month's 1991–2020 average, per province (also as % of normal).
3. **Explore past events.** Compare anomalies in strong El Niño years (1997–98, 2015–16, 2023–24) against neutral years; plot deficit vs. ONI strength.
4. **Model.**
   - Baseline: climatology (predict zero anomaly, i.e. a normal month).
   - Model A: linear regression on ONI lagged 0–6 months plus month-of-year.
   - Model B (stretch): gradient boosting on the same features.
5. **Evaluate.** Time-based splits only; hold out whole El Niño events (train without 2015–16, test on it, and so on).
6. **Forecast.** Feed current ONI and official ENSO outlooks into the chosen model to estimate February–May 2027 anomalies, with prediction intervals.

## Success criteria

The project succeeds if the model beats the climatology baseline on held-out El Niño events, decided before seeing any 2027 data.

- [ ] Lower mean absolute error (mm/month) than the baseline on at least 2 of 3 held-out El Niño events
- [ ] Predicts the correct sign (drier vs. wetter than normal) in most held-out dry-season months
- [ ] 80% prediction intervals contain the actual value about 80% of the time on held-out data
- [ ] Pipeline reruns end to end from a clean clone with one command
- [ ] Forecast for February–May 2027 published before February 2027

Open question: what MAE improvement counts as meaningful? Set the threshold after week 2's exploration, then freeze it.

## Scope

Version 1 covers rainfall only, for four provinces, with one baseline and one main model.

**In scope (v1)**

- Monthly rainfall anomaly for Cebu, Bohol, Negros Oriental, Siquijor
- ONI as the only climate predictor
- Climatology baseline and linear model
- Forecast for February–May 2027

**Out of scope (later versions)**

- Crop yields (PSA rice and corn production)
- Vegetation stress (MODIS NDVI)
- Temperature and heat index
- Typhoon effects and daily-scale extremes
- Other regions of the Philippines

## Deliverables

- [ ] **GitHub repo** with the reproducible pipeline, model code, tests, and a README (problem, architecture diagram, how to run)
- [ ] **Write-up** (blog post or README section) with 3–4 key charts: deficit vs. ONI, past-event comparison, model vs. baseline, 2027 forecast by province
- [ ] **Forecast table** of February–May 2027 anomalies per province, with intervals
- [ ] **Follow-up post** (mid-2027) comparing the forecast against observed rainfall

## Timeline

About 7 weeks at 8–10 hours a week, finishing by November 30, 2026. December stays free as a buffer, and the forecast is public two months before the February 2027 dry season.

1. **Oct 13–26: Data pipeline.** Set up the repo, ingest ONI and CHIRPS, clip to all four provinces, compute anomalies, set up scheduled runs; pull GSOD/GHCN station records as an optional cross-check.
2. **Oct 27 – Nov 8: Exploration.** Past-event comparisons and first charts; set and freeze the MAE threshold at the end of this phase.
3. **Nov 9–22: Modeling.** Baseline, linear model, event-holdout evaluation; add gradient boosting only if on schedule by Nov 16.
4. **Nov 23–30: Forecast and write-up.** February–May 2027 forecasts with intervals, README, charts, and published post.
5. **December: Buffer.** Absorbs any slip; if the pipeline phase runs long, cut the boosting stretch goal, not evaluation.
6. **Mid-2027: Validation.** Compare forecast with observed rainfall and publish the follow-up.

## Risks and mitigations

| Risk | Impact | Mitigation |
| --- | --- | --- |
| CHIRPS too coarse for small islands (Siquijor) | Noisy or biased province averages | Merge Siquijor with Negros Oriental, or cross-check with GSOD/GHCN station data |
| Only a handful of strong El Niño events since 1981 | Model overfits; wide intervals | Keep the model simple; report uncertainty honestly; use all ENSO years, not only El Niño |
| Typhoons and monsoon dominate some months | ONI explains little of the variance | Focus evaluation on the dry season; note it as a limitation |
| ENSO outlooks change month to month | 2027 forecast goes stale | Rerun the pipeline monthly and log each forecast version |
| Station records have gaps | Cross-check covers fewer years | Use only months with complete daily records; station data stays optional for v1 |
| Scope creep into crops and NDVI | Project never ships | Defer to v2 per the Scope section |
