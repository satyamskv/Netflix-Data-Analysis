# Data Dictionary — Netflix Dataset

Reference for every field in the dataset, after cleaning. The raw file lives in `Data/Netflix.xlsx`; the cleaned table is what the Power BI report loads.

## Fields

| Field | Data type | Description | Range / values | Missing |
|-------|-----------|-------------|----------------|---------|
| `id` | Text | Unique title identifier | Prefixed `tm` (movie) or `ts` (show); numeric part 1–7 digits | 0 |
| `title` | Text | Name of the movie or show | — | 0 |
| `type` | Text | Content type | `MOVIE`, `SHOW` | 0 |
| `description` | Text | Plot summary | Free text | 5 |
| `release_year` | Whole Number | Year of release | 1953–2022 | 0 |
| `age_certification` | Text | Audience rating | G, PG, PG-13, R, NC-17, TV-Y, TV-Y7, TV-G, TV-PG, TV-14, TV-MA | 2,285 |
| `runtime` | Whole Number | Length in minutes | 1–235 (0 replaced with null) | 18 (as null) |
| `imdb_score` | Decimal | IMDb rating | 1.5–9.6 | 0 |
| `imdb_votes` | Whole Number | Number of IMDb voters | 5–2,268,288 | 16 |
| `Rating_x_Votes` | Decimal | Custom: `imdb_score × imdb_votes` | — | 16 (where votes missing) |

## Fields removed during cleaning

| Field | Why it was removed |
|-------|--------------------|
| `index` | A plain row number (0–5282) with no analytical value |
| `imdb_id` | A unique IMDb reference code, never used in the analysis |

## Value reference

**`type`** — two values:
- `MOVIE` — 3,407 titles
- `SHOW` — 1,876 titles

**`age_certification`** — counts:

| Category | Titles |
|----------|--------|
| TV-MA | 792 |
| R | 548 |
| TV-14 | 436 |
| PG-13 | 424 |
| PG | 238 |
| TV-PG | 172 |
| G | 105 |
| TV-Y7 | 104 |
| TV-Y | 94 |
| TV-G | 72 |
| NC-17 | 13 |
| *(blank)* | 2,285 |

**`id` prefix** — the first two characters encode the type:
- `tm…` → movie
- `ts…` → show

## Notes on missing data

- **`age_certification` (2,285 blanks)** — no reliable way to infer a rating, so blanks are kept and excluded from certification visuals rather than treated as a category.
- **`imdb_votes` (16 blanks)** — rows are retained for their other data, but excluded from vote-based analysis and from `Rating_x_Votes`.
- **`runtime` (18 nulls)** — originally recorded as 0 for 18 shows (missing per-episode length); converted to null so they do not distort averages.
- **`description` (5 blanks)** — not used in visuals.
