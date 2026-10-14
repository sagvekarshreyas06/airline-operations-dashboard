# Airline Operations & Performance Intelligence Dashboard

A Power BI dashboard analysing **5.8 million U.S. domestic flights from 2015**: punctuality, delays, cancellations, airline performance, airport traffic and seasonal patterns.

Built as a third-year BSc Computer Science project.

## Dataset

[2015 Flight Delays and Cancellations](https://www.kaggle.com/datasets/usdot/flight-delays) (U.S. Department of Transportation, via Kaggle). Three files are used:

| File | Rows | Used for |
|---|---|---|
| `flights.csv` | 5,819,079 | One row per scheduled flight (fact table) |
| `airlines.csv` | 14 | Airline names |
| `airports.csv` | 322 | Airport names, city, state, latitude/longitude |

The CSV files are **not included** in this repository (`flights.csv` is 565 MB).

## How to open

1. Download the three CSV files from Kaggle into one folder.
2. Open `Airline_Operations_Dashboard.pbip` in Power BI Desktop.
3. *Transform data* → for the `flights`, `airports`, `airlines` and `Destination Airports` queries, change the **Source** step to your folder.
4. *Home → Refresh*.

The project is saved in Power BI Project (PBIP) format, so the report and data model are plain text files that can be version-controlled.

## Data model

A star schema with `flights` as the fact table:

| Table | Type | Relationship |
|---|---|---|
| `flights` | Fact | — |
| `airlines` | Dimension | `flights[AIRLINE]` → `airlines[IATA_CODE]` |
| `airports` | Dimension (origin) | `flights[ORIGIN_AIRPORT]` → `airports[IATA_CODE]` |
| `Destination Airports` | Dimension (destination, second copy of airports) | `flights[DESTINATION_AIRPORT]` → `Destination Airports[IATA_CODE]` |
| `Date` | DAX calendar for 2015 | `flights[Flight Date]` → `Date[Date]` |
| `Delay Causes` | 5-row helper table for the delay-cause chart | none (used by the `Delay Cause Minutes` measure) |

All relationships are many-to-one with single-direction filtering.

## Metric definitions

| Measure | Definition |
|---|---|
| Total Flights | All scheduled flights, including cancelled and diverted |
| Cancelled Flights | `CANCELLED = 1` |
| Diverted Flights | `DIVERTED = 1` |
| Completed Flights | Not cancelled and not diverted |
| On Time Flights | Completed flights arriving **less than 15 minutes** after schedule (U.S. DOT definition) |
| Delayed Flights | Completed flights arriving **15 or more minutes** late |
| On Time % | On Time Flights ÷ Completed Flights |
| Cancellation Rate | Cancelled Flights ÷ Total Flights |
| Avg Arrival Delay | Average arrival delay (minutes) of completed flights; early arrivals count as negative |
| Avg Departure Delay | Average departure delay (minutes) of completed flights; early departures count as negative |
| Delay cause minutes | Sum of the five delay-cause columns (Air System, Airline, Late Aircraft, Weather, Security) |

## Report pages

1. **Executive Overview**: headline KPIs, monthly flight volume and on-time trend, airline comparison and monthly delay composition.
2. **Airline Performance**: airline scorecard, departure vs arrival delay, volume vs punctuality scatter (bubble size = cancellation rate) and the delay-cause mix per airline.
3. **Network & Airports**: busiest origin and destination airports, airport map, top routes and route-level performance.
4. **Delay & Cancellation**: delay minutes by cause and by month, cancellation reasons, monthly cancellations and cancellation rate by airline.
5. **Time & Seasonal Analysis**: monthly trends, day-of-week patterns (Monday–Sunday), departure periods (Night, Morning, Afternoon, Evening) and the hourly delay pattern.
6. **Airline Detail** (drill-through): right-click an airline anywhere in the report → *Drill through → Airline Detail* for that airline's KPIs, monthly performance and delay analysis.

Airline, Month and Origin Airport slicers are synced across pages 1–5.

## Headline results (full year 2015)

- 5,819,079 flights; 5,714,008 completed.
- **81.4%** of completed flights arrived on time; **1.5%** of flights were cancelled (89,884) and 15,187 were diverted.
- Average arrival delay: 4.4 minutes (completed flights, early arrivals included).
- Weather was the most common cancellation reason (48,851 of 89,884 cancellations).
- Late-arriving aircraft caused the most delay minutes of the five recorded causes.
- American Eagle had the highest cancellation rate (5.1%).

## Known data limitations

- **October airport codes:** in the source file, all 486,165 October flights use 5-digit numeric airport IDs instead of 3-letter IATA codes. They do not match the airports table, so **airport-level visuals (airport names, map, routes, Origin Airport filter) exclude October**. Flight-level totals still include October.
- **Delay causes** are only recorded for flights delayed 15 minutes or more, so delay-cause minutes do not cover smaller delays.
- **Averages include early arrivals/departures** as negative minutes, which lowers the averages (Alaska Airlines averages −1.0 min arrival delay).

## Repository structure

```
Airline_Operations_Dashboard.pbip            open this file in Power BI Desktop
Airline_Operations_Dashboard.Report/         report pages and visuals (PBIR format)
Airline_Operations_Dashboard.SemanticModel/  tables, relationships and DAX measures (TMDL format)
```
