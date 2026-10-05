# F1 Race Analytics Pipeline

An automated pipeline that pulls Formula 1 timing data from the FastF1 API, stores it in PostgreSQL, and serves it to an interactive Power BI dashboard. It covers lap pace, driver consistency, tyre strategy and pit stops across six races: the Abu Dhabi and Miami Grands Prix, 2023 to 2025.

*Unofficial fan project, not affiliated with or endorsed by Formula 1. Formula 1 and F1 are trademarks of their respective owners.*

**Pipeline:** FastF1 API > Python analysis layer (pandas + OOP classes) > PostgreSQL > Power BI

## Dashboard

Screenshots of each page are attached as files

### Home
Landing page with navigation to the analysis pages.

### Overview
Race-level KPIs (winner, fastest lap, average lap time, top speed, laps, drivers, teams) and average lap time by lap number.

### Driver Analysis
Fastest lap and lap-time consistency by driver, with a driver data table and lap-by-lap trends.

### Tyre & Stint Analysis
Lap time by tyre age and compound, laps driven per compound for each driver, pit stops by lap, and a compound summary.

### Team Analysis
Average lap time, speed and race winner by team.

## Key findings

These come from six races at two circuits, so they describe those races, not a full season. The full set of eight findings, with caveats, is in the [documentation](docs/F1_Dashboard_Documentation_Siddarth_v2.pdf).

- **Raw lap times mislead.** Using all laps, the within-race standard deviation of lap time was 2.87 to 8.88 s. Using only clean laps (no pit or safety-car laps) it was 0.94 to 1.46 s. At Miami 2024, 96 laps were flagged as safety car and none of them is a clean lap.
- **Abu Dhabi teams stopped less each year.** Pit stops fell from 38 (2023) to 30 (2024) to 27 (2025), while average stint length rose from 20.3 to 21.6 to 24.6 laps. This is consistent with a shift toward fewer stops, though the data does not record strategy directly.
- **The fastest team on average did not always win.** The team with the lowest average clean lap won only 3 of the 6 races.
- **Safety cars bunch the stops.** At Miami 2024, seven drivers pitted on lap 28, under a safety car flagged on laps 28 to 32. No other race has more than four stops on one lap.

## Tech stack

| Layer | Tools |
|---|---|
| Data source | FastF1 API |
| Processing | Python 3.10, pandas, numpy |
| Persistence | PostgreSQL, SQLAlchemy, psycopg2 |
| Dashboard | Power BI Desktop (Power Query, DAX) |

## Repository structure

```
f1-race-analytics-pipeline/
├── notebooks/   Analysis notebook (ingestion, analysis classes, PostgreSQL load)
├── dashboard/   Power BI report (.pbix)
├── docs/        Project documentation (PDF)
├── images/      Architecture diagram and dashboard screenshots
├── .env.example Names of the required environment variables
└── environment.yml
```

## How to run

You need PostgreSQL, Python 3.10 (via conda) and Power BI Desktop.

1. Create the environment:
   ```bash
   conda env create -f environment.yml
   conda activate f1analysis
   ```
2. Copy `.env.example` to `.env` and fill in your own PostgreSQL connection details. The `.env` file is ignored by git and must never be committed.
3. Create an empty PostgreSQL database matching the name in your `.env`.
4. Open the notebook in `notebooks/` and run it for each race you want to load. Each race is written to its own table, and the `f1_all_sessions` view picks up new tables automatically.
5. Open `dashboard/Auto-F1_Project.pbix` in Power BI Desktop. The report data is already cached in the file, so it opens without a database. To refresh it, point the data source at your own database.

## Limitations

- Six races at two circuits. This is a proof of pipeline, not a full-season dataset.
- Miami 2025 is missing compound and stint data for 354 laps (laps 1 to 24, 15 of 20 drivers), so that race is left out of the tyre findings.
- Top speed is a speed-trap reading at one point on the track, not the car's true peak speed.
- Tyre findings are not fuel-corrected, so they cannot separate tyre wear from fuel burn.

## Author

Siddarth Varma Epuri
[LinkedIn](https://www.linkedin.com/in/siddarthve) | [GitHub](https://github.com/siddarth-hub)
