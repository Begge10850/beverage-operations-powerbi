# Methodology

## Scope

The report analyses operational records from 1 July 2022 to 28 February 2023. The source is a public research dataset from an industrial beverage filling line, not a named company dataset.

## Source structure

The source workbook contains four worksheets:

| Table | Grain | Main use |
| --- | --- | --- |
| `processed_hourly` | Hourly production interval | Production by hour |
| `daily_operation_summary` | Daily production summary | Litres produced and weighted daily efficiency |
| `hourly_operation_breakdown` | Hourly operating interval | Operating time, monitored time, downtime, and efficiency |
| `downtime_event_log` | Individual downtime event | Event counts, duration, distribution, and longest event |

The Power BI model adds a calculated `Calendar` table. All four source tables relate many-to-one to the calendar, allowing one date slicer to filter the full report consistently.

## Preparation

The model uses the source's standardised date and time fields. The report-facing logic additionally:

- expresses hourly production in litres by converting the source's gallon count with the exact US-gallon factor `3.785411784`;
- analyses downtime duration in minutes;
- derives weekday labels and weekday sort order from the calendar;
- groups downtime events into five duration bands;
- uses a separate numeric band-order field to preserve logical category order.

## Calculation choices

Operational efficiency is calculated as:

```text
sum of operating time / sum of monitored time
```

This weighted calculation is preferable to averaging row-level percentages because it respects the duration represented by each row. Daily and hourly views use the corresponding daily or hourly operating and monitored-time fields.

Average downtime per event divides total downtime minutes by the distinct event count. The longest event uses the maximum event duration. Average daily production iterates over visible calendar dates and evaluates daily litres in the current filter context.

## Visual design

The report uses four task-oriented pages rather than mixing all questions on one canvas:

- Overview for headline KPIs and monthly movement
- Downtime for event frequency, duration, and distribution
- Production for weekday and hourly patterns
- Efficiency for weighted performance patterns

All pages use the same date range control and a consistent blue/black palette. KPI cards provide context before the detailed charts. Dual-axis charts are used only where production/downtime or downtime/events must be compared across the same time period; each series retains a labelled axis and legend.

## Validation evidence

The repository's report PDF was exported directly from Power BI Service with current values across all four pages. The screenshots were rendered from that export and visually checked for clipping, overlap, unreadable labels, or missing visuals. The semantic model table names, relationships, fields, and DAX definitions were inspected directly in Power BI Service.

## Limitations

- The dataset covers one filling-line context and eight months; patterns should not be generalised to all beverage plants.
- The public source does not identify the operating company or facility.
- The analysis is descriptive. It does not establish causal drivers of downtime or efficiency.
- The report contains no equipment, fault-reason, shift, or operator dimension, so root-cause analysis is outside its scope.
- “Gallon” in source field names reflects source-system terminology; the report converts production measures to litres for presentation.
