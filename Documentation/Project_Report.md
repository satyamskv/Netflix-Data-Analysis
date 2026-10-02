# Project Report — Netflix Movies & TV Shows Analysis

This document records the full analytical process behind the dashboard: the data, the cleaning decisions, the measures, the visual for each objective, and the findings and limitations.

---

## 1. Business Context

Netflix's catalogue spans seven decades of film and television. This analysis asks six questions a content or strategy team might raise about the library — which titles are best, how the catalogue has grown, whether quality is trending up or down, how audience engagement has changed, how runtimes have shifted, and whether age certification relates to ratings.

---

## 2. Data Understanding

The dataset contains **5,283 titles** — 3,407 movies and 1,876 shows — released between **1953 and 2022**.

| Field | Description | Notes |
|-------|-------------|-------|
| `id` | Unique identifier | Prefixed `tm` (movie) or `ts` (show) |
| `title` | Name of the title | Some non-English titles carry accents |
| `type` | MOVIE or SHOW | Two values only |
| `description` | Plot summary | Free text; not used in visuals |
| `release_year` | Year of release | 1953–2022; some years absent |
| `age_certification` | Rating category | Missing in ~43% of rows |
| `runtime` | Length in minutes | 18 shows recorded as 0 |
| `imdb_score` | IMDb rating | 1.5–9.6 |
| `imdb_votes` | Number of IMDb voters | Missing in 16 rows |

**Early observation:** the data is dominated by recent titles. Nearly nine in ten titles (4,698 of 5,283) were released in 2010 or later, and only 216 predate 2000. This skew is the single most important caveat in the whole analysis and is referenced throughout.

---

## 3. Data Cleaning & Transformation (Power Query)

1. **Removed `index`** — a plain row number (0–5282) with no analytical value.
2. **Removed `imdb_id`** — a unique reference code never needed for visuals.
3. **Added `Rating_x_Votes`** — a custom column equal to `imdb_score × imdb_votes`. Rationale: a rating from very few voters is unreliable, so combining score with vote count produces a more meaningful "best title" measure than score alone.
4. **Replaced `runtime = 0` with null** — 18 shows had a runtime of 0, meaning the per-episode length was simply unrecorded. Treating these as blanks keeps average-runtime figures honest.
5. **Repaired text encoding** — some titles and descriptions were saved with the wrong encoding, turning accented characters into garbled text (e.g. "Pokémon" appeared as "PokÃ©mon"). These were corrected.
6. **Set correct data types** — numeric fields cast to Whole Number or Decimal as appropriate.
7. **Left genuine gaps untouched** — missing `age_certification` and `imdb_votes` were not filled or deleted, since there is no reliable way to infer them and the rows carry valid data elsewhere.

---

## 4. Measures (DAX)

| Measure | Definition | Purpose |
|---------|-----------|---------|
| Total Titles | `DISTINCTCOUNT(Netflix[id])` | Count of unique titles |
| Total Movies | `CALCULATE([Total Titles], Netflix[type] = "MOVIE")` | Movie count |
| Total Shows | `CALCULATE([Total Titles], Netflix[type] = "SHOW")` | Show count |
| Avg IMDb Rating | `AVERAGE(Netflix[imdb_score])` | Mean rating |
| Avg Runtime | `AVERAGE(Netflix[runtime])` | Mean runtime (minutes) |
| Total Votes | `SUM(Netflix[imdb_votes])` | Aggregate votes |
| Best Rating x Votes | `MAX(Netflix[Rating_x_Votes])` | Peak score × votes |

---

## 5. Visuals & Findings

### Objective 1 — Best movie and show overall
**Visual:** Horizontal bar chart of `title` vs `Rating_x_Votes`, split by type.
**Finding:** *Inception* is the best-rated-and-popular movie (~19.96M) and *Breaking Bad* the best show (~16.4M). *Forrest Gump* and *Django Unchained* follow.

### Objective 2 — Number of titles over the years
**Visual:** Area chart of Movies Count and Shows Count by `release_year`.
**Finding:** Strong upward trend, concentrated after 2010. The dataset is **not** balanced across the decades — it is skewed to recent years.

### Objective 3 — Average IMDb score over time
**Visual:** Line/area chart of `Avg IMDb Rating` by `release_year`.
**Finding:** A **gentle decline** from roughly 7.1 (1950s) to 6.3 (2020s). Early-year figures are volatile because those years contain very few titles.

### Objective 4 — Total IMDb votes over time
**Visual:** Line/area chart of `Total Votes` by `release_year`.
**Finding:** Total votes climb sharply — but this is driven mainly by the growing number of titles, not by more voting per title. The average votes per title actually falls over time, so this chart is best treated as a caveat rather than a headline insight.

### Objective 5 — Average runtime over time
**Visual:** Line/area chart of `Avg Runtime` by `release_year`.
**Finding:** Runtime has **broadly decreased** — from about 130 minutes in the 1960s to about 75 minutes in the 2020s — and become more consistent. Again, few early data points limit confidence in the earliest decades.

### Objective 6 — Age certification vs rating
**Visual:** Column chart of `Avg IMDb Rating` by `age_certification`, split by type.
**Finding:** On average **shows rate higher than movies** (~7.0 vs ~6.3). Within shows, **TV-14** is the highest-rated category; within movies, **PG-13** leads. Titles with no certification are excluded, as they are not a real category.

---

## 6. Dashboard Design

- **KPI cards:** Total Titles, Total Movies, Total Shows, Avg IMDb Rating, Avg Runtime.
- **Slicers:** Type, Release Year, Age Certificate, IMDb Score, IMDb Votes.
- **Theme:** dark theme for readability and a consistent look across visuals.
- The layout moves from summary (cards) to trends (time series) to comparison (certification), so the page reads as a story.

---

## 7. Limitations

- The **recency skew** limits conclusions about earlier decades.
- **2022 is a partial year**, so its totals are understated.
- **Missing certifications (43%)** reduce the sample for the certification analysis.
- **Duplicate titles** (46) are genuinely distinct titles, not data errors.
- Average-based trends in the earliest years rest on very few titles and should be read with caution.

---

## 8. Conclusion

The dashboard answers all six questions and shows a catalogue that has grown explosively since 2010 while average ratings and runtimes have drifted gently downward. The most actionable comparison is certification vs rating, which cleanly separates the performance of movies and shows.
