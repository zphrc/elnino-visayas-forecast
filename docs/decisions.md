# Decisions

| # | Date | Decision | Status |
| --- | --- | --- | --- |
| 1 | 2026-10-13 | [Use NOAA GSOD/GHCN instead of PAGASA station data](#1-use-noaa-gsodghcn-instead-of-pagasa-station-data) | Accepted |

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