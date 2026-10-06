# DataZoe.SVG

SVG-based visuals authored as DAX user-defined functions. Drop these into any Power BI semantic model and use the returned `data:image/svg+xml;utf8,...` string in a table/matrix column formatted as **Image URL**, or in a card visual.

## Functions

### `DataZoe.SVG.TrendSVG`

A compact inline trend / sparkline chart. Grain-agnostic — works with any date/period column (day, month, quarter, year).

**Signature**

```dax
DataZoe.SVG.TrendSVG(
    valueExpression,            // measure or scalar expression to plot
    periodDateColumn,           // date/period column (e.g. 'Date'[Month])
    numberOfPeriods,            // most-recent N periods to include (e.g. 12)
    axisLabelCharactersToShow,  // characters to keep from the formatted period label (e.g. 3 → "JAN")
    dateFormatString,           // FORMAT string for axis labels (e.g. "MMM", "yyyy")
    valueFormatString,          // FORMAT string for value data labels (e.g. "#,0", "$#,0", "0.0%")
    lineColor,                  // hex color for the line/markers (e.g. "#0078D4")
    hover                       // TRUE to include shading and value labels, FALSE for minimal
)
```

**Example**

```dax
Sales Trend =
DataZoe.SVG.TrendSVG(
    [Total Sales],
    'Date'[Month],
    12,
    3,
    "MMM",
    "$#,0",
    "#0078D4",
    TRUE
)
```

Add the measure to a table/matrix column and set the column's **Data category** to *Image URL*.

## License

MIT
