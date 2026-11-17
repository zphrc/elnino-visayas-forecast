# Roadmap

A step-by-step checklist for the project, based on [the proposal](proposal.md). Each step lists what to build, how to know it's done, and keywords to search if you get stuck.

**How to use this:** work top to bottom, tick boxes as you go, and commit at every 💾. If a step takes more than one session, that's normal. Try for 20–30 minutes before asking for help.

---

## Part 1: How strong is this El Niño? (target: Oct 26)

### Data and labels
- [x] Load ONI from NOAA into a DataFrame
- [x] Convert seasons to real dates (`datetime64`), with a check that no dates are missing
- [x] Round ONI to one decimal (NOAA precision) and label each month El Niño / La Niña / Neutral
- [x] Number consecutive runs of the same label 💾

### Events
- [x] Summarize runs into a `runs` table (start, end, months, label)
- [x] Filter to real events: El Niño or La Niña lasting 5+ months
- [ ] Check: 1997–98, 2015–16, and 2023–24 El Niños all appear in `events` 💾
- [ ] Add **peak ONI** to each run. For El Niño the peak is the highest value; for La Niña, the lowest.
  - *Done when:* 1997–98 and 2015–16 both peak around +2.4 to +2.6
  - *Search:* pandas named aggregation with max and min; selecting a value based on another column
- [ ] Add a **strength** column: weak (0.5–0.9), moderate (1.0–1.4), strong (1.5–1.9), very strong (≥2.0), using the absolute peak
  - *Search:* `np.select`; `abs()`
- [ ] Find the **2026 event**. It's still ongoing, so its end date is just the latest month in the data. Note that in your write-up.
  - *Done when:* you can say how long it has lasted so far and its peak so far 💾

### Chart
- [ ] Plot ONI over time, 1950 to now
- [ ] Add horizontal lines at +0.5 and −0.5
- [ ] Shade El Niño events (and optionally La Niña) using the `events` table
- [ ] Highlight 1997–98, 2015–16, 2023–24, and 2026
- [ ] Add a title, axis labels with units (°C), and a source note ("Data: NOAA CPC")
  - *Search:* matplotlib `axhline`, `axvspan`, `annotate`
- [ ] Save as `docs/images/oni-history.png` 💾

### Move code out of the notebook
- [ ] Create `src/elnino_visayas_forecast/oni.py` with:
  - `download_oni()`: saves the raw file to `data/raw/oni.txt`
  - `parse_oni(text)`: returns the tidy DataFrame (dates, rounded ONI, labels)
  - `find_events(df)`: returns the events table
- [ ] Write tests in `tests/test_oni.py` using a few pasted lines of the real file
  - *Done when:* `uv run pytest` passes, and tests cover the season-to-month mapping, rounding, and one known event
  - *Search:* pytest basics; pytest fixtures
- [ ] Save the ONI table to DuckDB
  - *Search:* duckdb python insert pandas DataFrame
- [ ] Run Ruff: `uv run ruff check` and `uv run ruff format` 💾

### Write-up
- [ ] Add 3–5 sentences to the README: how does 2026 compare to past events so far?
- [ ] Add the chart to the README
- [ ] Tick Part 1 in the README progress list 💾
- [ ] Optional: add the chart to your GitHub profile README as a clickable image

---

## Part 2: What did past El Niños do to rainfall? (target: Nov 8)

### Get rainfall for one province first
- [ ] Download CHIRPS v2 **monthly** rainfall covering the Philippines
  - *Search:* CHIRPS v2.0 monthly global netCDF; `xarray` open_dataset
- [ ] Download Philippine province boundaries
  - *Search:* HDX Philippines administrative boundaries; `geopandas` read_file
- [ ] Average rainfall over **Cebu** for every month since 1981
  - *Done when:* you have a table of date and rainfall (mm) for Cebu, and the wet and dry seasons are visible when plotted
  - *Search:* `rioxarray` clip; zonal statistics; `regionmask`
- [ ] Log any tool choices in `decisions.md` 💾

### Expand and compute anomalies
- [ ] Repeat for Bohol, Negros Oriental, and Siquijor
- [ ] Check Siquijor's results; it's small, so see whether it covers enough grid cells (see Risks in the proposal)
- [ ] Compute **normal rainfall** for each province and calendar month, using 1991–2020
- [ ] Compute **anomaly** = rainfall − normal (also as % of normal)
  - *Search:* pandas groupby transform; groupby by month
- [ ] Join rainfall anomalies with ONI on date 💾

### Explore
- [ ] Compare dry-season (Feb–May) anomalies in El Niño years vs. neutral years
- [ ] Chart: rainfall deficit vs. peak ONI strength, one point per past event
- [ ] Chart: rainfall anomalies during 1997–98, 2015–16, and 2023–24
- [ ] Look at timing: how many months after ONI rises does rainfall drop?
  - *Search:* lagged correlation; pandas `shift`
- [ ] **Set and freeze the MAE threshold** for success; log it in `decisions.md` 💾
- [ ] Optional: GSOD/GHCN station cross-check for Mactan

---

## Part 3: Can we forecast it? (target: Nov 22)

- [ ] Build features: ONI lagged 0–6 months, plus calendar month
- [ ] Baseline: predict zero anomaly (a normal month)
- [ ] Model A: linear regression
  - *Search:* scikit-learn `LinearRegression`; one-hot encoding
- [ ] Evaluate by **holding out whole El Niño events**: train without one event, test on it, repeat
  - *Search:* time series cross-validation; leave-one-group-out
- [ ] Report MAE for the baseline and model on each held-out event
- [ ] Check sign accuracy (drier vs. wetter) in dry-season months
- [ ] Add prediction intervals and check their coverage (~80%)
  - *Search:* quantile regression; prediction intervals from residuals
- [ ] Compare results against the frozen success criteria 💾
- [ ] Optional, only if on schedule by Nov 16: gradient boosting

---

## Part 4: The 2027 forecast (target: Nov 30)

- [ ] Get the latest official ENSO outlook for the coming months
  - *Search:* IRI ENSO forecast; NOAA CPC ENSO outlook
- [ ] Forecast Feb–May 2027 rainfall anomalies per province, with intervals
- [ ] Make a forecast table and chart (or map) by province
- [ ] Write the full write-up: question, data, method, results, limitations
- [ ] Update the README and tick Part 4
- [ ] Publish (blog post, LinkedIn, or README section) 💾
- [ ] Optional: update your profile README visual

---

## Follow-up (mid-2027)

- [ ] Download observed Feb–May 2027 rainfall
- [ ] Compare it with the forecast, using the same metrics
- [ ] Publish the follow-up, including what the model got wrong
