# Environmental Risk Assessment: Power BI Dashboard

An interactive Power BI report that assesses environmental risk from sea surface temperature (SST) and pH, and shows how that risk changed across seven marine locations from 2015 to 2023.

## Business question

Which reefs carry the highest risk, what conditions go with that risk, and how did risk change between years?

## Dataset

- **Source:** https://www.kaggle.com/datasets/atharvasoundankar/shifting-seas-ocean-climate-and-marine-life-dataset?utm_source=chatgpt.com
- **Size:** 500 readings from 7 locations: Great Barrier Reef, Red Sea, South China Sea, Hawaiian Islands, Caribbean Sea, Galapagos and Maldives.
- **Period:** 1 January 2015 to 31 December 2023, about 55 readings per year.
- **Fields:** date, location, latitude, longitude, SST (°C), pH level, bleaching severity, species observed, marine heatwave flag, and hemisphere (from a second sheet).

## Data cleaning

- Removed a pivot table that had been pasted beside the data.
- Fixed the SST header encoding (`SST (Â°C)` renamed to `SST_C`).
- Read dates as day/month/year (English UK). About 40% of days are 12 or less, so the locale matters.
- Labelled the 150 blank bleaching-severity cells as "None".
- Replaced a constant "Alkaline" pH-type column with pH bands.
- Added hemisphere from the Location lookup.

## Method

### Risk score

Each reading gets two ranked scores, each from 1 to 10, based on its percentile within the dataset:

- **Temperature score:** higher SST gives a higher score.
- **Acidity score:** lower pH gives a higher score.

**Risk score = temperature score + acidity score**, which ranges from 2 to 20.

| Risk rating | Score range | Readings |
|---|---|---|
| Low | 2 to 10 | 233 |
| Moderate | 11 to 14 | 127 |
| High | 15 to 18 | 102 |
| Extreme | 19 to 20 | 38 |

### pH bands

| Band | pH |
|---|---|
| Normal | 8.10 and above |
| Reduced | 8.00 to under 8.10 |
| Acidifying | 7.95 to under 8.00 |
| Strongly acidifying | under 7.95 |

The cut-offs are analyst-defined, relative to the roughly 8.2 pre-industrial surface pH.

### High+Extreme %

The share of readings rated High or Extreme within the current filter.

## Key findings

- **2020 was the highest-risk year.** Average risk was 12.3, and 40% of readings were High or Extreme.
- **Risk fell in 2021.** Average risk dropped to 10.6, and High or Extreme readings fell to 26.8%. Average species observed rose from 117.0 to 122.5.
- **The 2021 drop did not last.** Risk rose to 11.4 in 2022, then was 10.3 in 2023.
- **Two locations moved the other way.** Hawaiian Islands rose from 11.2 to 13.5, and Maldives rose from 10.0 to 11.6. The other five locations fell.
- **Caribbean Sea has the highest average risk** overall, at 11.9.
- **Species observed falls as risk rises:** 130 at Low, 118 at Moderate, 109 at High and 98 at Extreme.
- **Species observed falls as pH becomes more acidic:** 130 at Normal, down to 101 at Strongly acidifying.

## Scope and next steps

- **Scope of the score.** Ratings compare readings within this dataset, so they rank risk rather than set a coral-safety threshold. Next step: compare against published ecological limits.
- **Heatwave flag.** A heatwave is defined as SST of 30 °C or above, so it tracks the same signal as temperature. Next step: define heatwaves against a baseline for each location.
- **Bleaching records.** Severity is recorded for 350 of 500 readings, and bleaching is reported alongside the score rather than inside it. Next step: complete bleaching records, which would allow a formal test of heat and bleaching.
- **Sample size.** About 55 readings per year reveal clear patterns. Year-to-year changes are best read as direction rather than proof. Next step: more readings per location and year.
- **Dataset.** The patterns are unusually regular, so the project is best read as a method demonstration. Next step: rerun the same workflow on observed monitoring data.

## Dashboard pages

The full guide to each page is in `Environmental_Risk_Assessment_Dashboards.pdf`.

### 1. Overview



Headline KPIs, readings by year and risk rating, the average risk trend, and a map of the seven locations.

### 2. Drivers



SST against species observed, species by risk rating and by pH band, and average risk by location.

### 3. 2020 vs 2021



Year-over-year cards, the location matrix, the monthly trend, and High+Extreme % by location.

### 4. Location Deep Dive (drill-through)



Right-click any location on another page and choose Location Deep Dive to open this page. It shows SST over time against the 30 °C threshold, the High and Extreme readings, and the bleaching mix.

### 5. Tooltips

Definitions of High+Extreme % and the risk score.

## DAX measures

Measures are kept in a `DAX_table` table:

- `Readings`, `Average SST`, `Average Ph`, `Avg Species`, `Average Risk rating`, `Heatwave %`
- `High+Extreme Readings`, `High+Extreme %`, `High+Extreme % PY`, `High+Extreme Change (pts)`
- `Risk PY`, `Risk Change`, `Risk Change %`
- `Species PY`, `Species Change %`

The year-over-year measures use `SAMEPERIODLASTYEAR` on a `calendar` table marked as the date table. Select one year in the Year slicer when using them.

## How to reproduce

1. Load `risk_r_cleandt.csv`, or start from the raw workbook and apply the cleaning steps above.
2. In Power Query, set the Date column to English (UK) and rename `SST (°C)` to `SST_C`.
3. Create a `calendar` table and mark it as the date table.
4. Add calculated columns: `pH type`, `pH Order` and `Risk Rank`.
5. Add the measures listed above.
6. Build the pages. On the Location Deep Dive page, add `Location` as the drill-through field.

## Files

- `Ocean_Risk_Dashboard.pbix`: the Power BI report
- `risk_r_cleandt.csv`: the cleaned data
- `screenshots/`: images of each dashboard page
- `Environmental_Risk_Assessment.pdf`: project documentation covering descriptive, diagnostic, predictive and prescriptive analysis
- `Environmental_Risk_Assessment_Dashboards.pdf`: the four dashboard pages with a guide to each

## Tools

Power BI Desktop, Power Query, DAX.

## Author

Bharat Kumaar R
