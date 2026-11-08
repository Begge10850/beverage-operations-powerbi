# Measures and calculated fields

The following DAX measures were read directly from the Power BI Service semantic model on 9 November 2026.

## Production

```DAX
Average Daily Production (L) =
AVERAGEX(
    VALUES('Calendar'[Date]),
    CALCULATE(
        SUM('daily_operation_summary'[liters_produced])
    )
)
```

```DAX
Hourly Production (L) =
SUM(processed_hourly[production_gallons]) * 3.785411784
```

Total production cards and charts use the sum of `daily_operation_summary[liters_produced]`.

## Operational efficiency

```DAX
Weighted Operational Efficiency =
DIVIDE(
    SUM(daily_operation_summary[operation_time_dec]),
    SUM(daily_operation_summary[monitored_time_dec])
)
```

```DAX
Operational Efficiency (%) =
DIVIDE(
    SUM('hourly_operation_breakdown'[operation_time_h]),
    SUM('hourly_operation_breakdown'[monitored_time_h])
)
```

Both measures calculate a ratio of summed operating time to summed monitored time. This avoids the distortion caused by taking an unweighted average of row-level efficiency percentages.

## Downtime

```DAX
Average Downtime per Event =
DIVIDE(
    SUM(downtime_event_log[downtime_minutes]),
    DISTINCTCOUNT(downtime_event_log[downtime_id])
)
```

```DAX
Longest Downtime Event =
MAX(downtime_event_log[downtime_minutes])
```

The remaining downtime KPIs use direct aggregations:

- **Total Downtime (min):** sum of `downtime_event_log[downtime_minutes]`
- **Downtime Events:** distinct count of `downtime_event_log[downtime_id]` (the report card displays the count of the ID field)

## Duration bands

The downtime distribution groups event durations into the following ordered bands:

1. Under 5 min
2. 5-15 min
3. 15-30 min
4. 30-60 min
5. 60+ min

`Duration Band Order` is used as the numeric sort key so the categories remain chronological rather than alphabetical.

## Date attributes

The calculated `Calendar` table supplies `Date`, `Day Name`, and `Day Number`. `Day Number` sorts weekday names from Monday to Friday, and the calendar has one-to-many relationships to each source table.
