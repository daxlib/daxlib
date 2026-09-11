# DataTides.AltText

Accessibility-focused DAX functions for generating dynamic, screen-reader-friendly alt text in Power BI reports. Covers common visual types (bullet charts, progress bars, rating scales, sparklines, status pills, line charts) plus generic KPI narrative and context helpers that work with any measure.

## Usage

```dax
AltTextNarrative = 
DataTides.AltText.ChangeNarrative(
    "Total Sales",                                                   // MetricLabel
    SUM(Sales[Amount]),                                              // CurrentValue
    CALCULATE(SUM(Sales[Amount]), DATEADD(Date[Date], -1, MONTH)),   // PreviousValue
    FORMAT(SUM(Sales[Amount]), "£#,0.0")
)
                            
AltTextContext =                            
DataTides.AltText.Context(
    "Category", SELECTEDVALUE(Category[Category]),               // Label1Name, Label1Value
    "Department", SELECTEDVALUE(Department[Department]),         // Label2Name, Label2Value
    MIN(Date[Date]), MAX(Date[Date])                             // StartDate, EndDate
)      

AltTextLineChart =
DataTides.AltText.LineChart(
    "the last 12 months",       // TrendPeriod
    "Revenue, Cost",            // MeasuresText
    BLANK(),                    // CategoryText
    BLANK(),                    // FiltersText
    "GBP, thousands",           // UnitsText
    [Revenue Change %],         // RevChange
    [Cost Change %],            // CostChange
    [Revenue Current Month],    // RevCurrent
    [Revenue Previous Month],   // RevPrev
    [Cost Current Month],       // CostCurrent
    [Cost Previous Month],      // CostPrev
    [Revenue YTD],              // RevYTD
    [Cost YTD]                  // CostYTD
)
```

## Functions

- **ChangeNarrative** — a KPI value with an optional period-over-period comparison and optional context
- **Context** — a standardised context sentence from two label/value pairs and a date range
- **LineChart** — Revenue/Cost trend narrative with month-over-month and year-to-date insights

## Documentation

- See the `manifest.daxlib` and `lib/functions.tmdl` files for full parameter and return details.


## License

This project is licensed under the MIT License.
