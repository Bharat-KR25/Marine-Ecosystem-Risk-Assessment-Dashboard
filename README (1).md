# Environmental Risk Assessment: Power BI Dashboard

An interactive Power BI report that assesses environmental risk from sea surface temperature (SST) and pH, and shows how that risk changed across seven marine locations from 2015 to 2023.

![Overview page](screenshots/overview.png)

## Business question

Which reefs carry the highest risk, what conditions go with that risk, and how did risk change between years?

## Dataset

- **Source:** [add the dataset source or link here]
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

## Limitations

- **Scores are relative.** Ratings compare readings within this dataset. They are not a coral-safety threshold.
- **The heatwave flag repeats the SST score.** In this data, marine heatwave is true exactly when SST is 30 °C or above, so it adds no separate information.
- **Bleaching is not part of the score.** Bleaching severity shows little relationship with SST here, so this project makes no claim that heat causes bleaching.
- **Samples are small.** About 55 readings per year means year-to-year changes show patterns, not proof.
- **The data looks synthetic.** Its patterns are cleaner than most real monitoring data, so treat the findings as an analysis exercise.

## Dashboard pages

1. **Overview:** headline KPIs, risk map, risk mix by year, and average risk trend.
2. **Drivers:** SST against species scatter, species by risk rating, species by pH band, and average risk by location.
3. **2020 vs 2021:** year-over-year cards, location matrix, monthly trend, and High+Extreme % by location.
4. **Location Deep Dive (drill-through):** right-click any location on another page to open it. Shows SST over time against the 30 °C threshold, the High and Extreme readings, and the bleaching mix.
5. **Tooltips:** definitions of High+Extreme % and the risk score.

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

## Tools

Power BI Desktop, Power Query, DAX.

## Author

[Your name]
