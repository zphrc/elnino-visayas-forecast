# Decisions

| # | Date | Decision | Status |
| --- | --- | --- | --- |
| 1 | 2026-10-13 | [Use NOAA GSOD/GHCN instead of PAGASA station data](#1-use-noaa-gsodghcn-instead-of-pagasa-station-data) | Accepted |
| 2 | 2026-10-14 | [Round ONI to one decimal before labeling](#2-round-oni-to-one-decimal-before-labeling) | Accepted |S

---

## 2026-10-13: Use NOAA GSOD/GHCN instead of PAGASA station data

**Status:** Accepted

**Context:** The proposal planned PAGASA station rainfall as an optional cross-check for CHIRPS. PAGASA's raw data requests for non-students (form CADS-01A) require a fee. The free student route (CADS-02A) needs a current school ID and an approved thesis proposal, which don't apply to a personal portfolio project.

**Decision:** Drop PAGASA. Use NOAA GSOD/GHCN-Daily station records as the optional cross-check instead.

**Alternatives considered:**
- Pay the PAGASA fee: rejected; goal is to keep the project free
- Skip station data entirely: viable, since CHIRPS covers v1 needs, but a free cross-check adds credibility
- PAGASA student route: not eligible

**Impact:** No change to v1 scope. GSOD/GHCN records may have gaps, so only months with complete daily data will be used. Daily values must be aggregated to monthly.

---

## 2. Round ONI to one decimal before labeling

**Date:** 2026-10-14
**Status:** Accepted

**Context:** The NOAA ONI data file gives values to two decimals, but NOAA's
official ONI table rounds them to one decimal and labels El Niño / La Niña
from the rounded values. Labeling from the raw values split some periods
that NOAA treats as continuous (e.g., Feb–Apr 1953: 0.50, 0.48, 0.56).

**Decision:** Round ONI to one decimal before assigning phase labels.

**Alternatives considered:**
- Use raw two-decimal values: rejected; events wouldn't match NOAA's record

**Impact:** Phase labels and run numbers change slightly. Event list should
now match NOAA's official El Niño and La Niña episodes.