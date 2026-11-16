# EG.Histogram

Functions to build a **histogram** in Power BI with pure DAX: a calculated table of equal-width bins and a measure that counts the items in each bin. The bin edges can be fixed (a business specification) or follow the data (minimum and maximum of a column).

## What it does

`EG.Histogram.Bins` generates the bins table: `<binCount>` bins of equal width between `<minValue>` and `<maxValue>`, each with an index, a lower edge, an upper edge and a text label for the axis of the visual.

`EG.Histogram.FrequencyInRange` counts, for the bin in the current row of the visual, the items whose value falls between the `Lower` and `Upper` edges of that bin:

> **Note:** Blank values are ignored (e.g., months without sales are not counted), and values outside the range of the bins table are not counted in any bin. The count respects every filter of the report (slicers, legend, rows), because `<itemsTable>` is evaluated in the current filter context.

---

## Functions

### `EG.Histogram.Bins` — calculated table

```DAX
EG.Histogram.Bins (
    minValue,           -- lower edge of the first bin, e.g., 3.43 or MIN ( Parts[Thickness (mils)] )
    maxValue,           -- upper edge of the last bin, e.g., 3.60 or MAX ( Parts[Thickness (mils)] )
    binCount,           -- number of bins, e.g., 17
    [formatString]      -- (optional) format of the Label column (default: "General Number")
)
```

**Return:** a table with the columns `Bin` (index from 1 to `<binCount>`), `Lower`, `Upper` and `Label` (e.g., `"3.43 to 3.44"`).

### `EG.Histogram.FrequencyInRange` — measure

```DAX
EG.Histogram.FrequencyInRange (
    itemsTable,         -- the items to count, e.g., Parts or VALUES ( Customer[Key] )
    valueExpr,          -- value of each item, evaluated in the row context of itemsTable, e.g., Parts[Thickness (mils)]
    lowerColumn,        -- lower edge column of the bins table, e.g., 'Thickness Bins'[Lower]
    upperColumn         -- upper edge column of the bins table, e.g., 'Thickness Bins'[Upper]
)
```

**Return:** the number of items whose value falls in the bin of the current filter context, or `0` when there is none.

---

## Model requirements

### The bins table — a disconnected calculated table

Create the table returned by `EG.Histogram.Bins` as a **calculated table** with no relationships to other tables. Set **Sort by column** of `Label` to `Bin` (a manual step in the model), so the bars follow the order of the bins and not the alphabetical order of the labels.

### `<valueExpr>` — column, measure or expression

`<valueExpr>` accepts:

- a column: `Parts[Thickness (mils)]`;
- a measure: `[Average Handle Time (s)]` (with context transition);

A bare aggregation does not work alone: use `CALCULATE ( SUM ( Parts[Thickness (mils)] ) )`, not `SUM ( Parts[Thickness (mils)] )`. With `CALCULATE ( SUM ( ... ) )` over a table without a unique key, identical rows are summed together, so prefer passing the column.

---

## Basic usage

### Static bins

Use fixed edges when there is a business limit to respect, such as a thickness specification from 3.45 to 3.55. The bins stay the same regardless of the data:

```DAX
Thickness Bins = EG.Histogram.Bins ( 3.43, 3.60, 17, "0.00" )    -- calculated table
```

| Bin | Lower | Upper | Label |
| ---: | ---: | ---: | --- |
| 1 | 3.43 | 3.44 | 3.43 to 3.44 |
| 2 | 3.44 | 3.45 | 3.44 to 3.45 |
| 3 | 3.45 | 3.46 | 3.45 to 3.46 |
| ... | ... | ... | ... |
| 16 | 3.58 | 3.59 | 3.58 to 3.59 |
| 17 | 3.59 | 3.60 | 3.59 to 3.60 |

### Dynamic bins

Use the minimum and maximum of the data as edges when the range is not known in advance. The bins cover the full range of values and adjust to new data on every refresh:

```DAX
Thickness Bins =    -- calculated table
EG.Histogram.Bins ( MIN ( Parts[Thickness (mils)] ), MAX ( Parts[Thickness (mils)] ), 17, "0.00" )
```

### Counting the items per bin

The same measure works with both bins tables:

```DAX
Parts per Bin =
EG.Histogram.FrequencyInRange (
    Parts,
    Parts[Thickness (mils)],
    'Thickness Bins'[Lower],
    'Thickness Bins'[Upper]
)
```

### Result

Build a column chart with `'Thickness Bins'[Label]` on the X axis and `Parts per Bin` on the Y axis.

With `Thickness Bins = EG.Histogram.Bins ( 3.0, 3.9, 9, "0.0" )`, 2,000 parts and `Parts[Shift]` in the legend, the chart looks like this:

```text
 600 ┤
     │                                        553
     │                                      ░░░░░░░
     │                                      ░░░░░░░
 480 ┤                            436       ░░░░░░░       442
     │                          ░░░░░░░     ░░░░░░░     ░░░░░░░
     │                          ░░░░░░░     ░░░░░░░     ░░░░░░░
     │                          ░░░░░░░     ░░░░░░░     ░░░░░░░
 360 ┤                          ░░░░░░░     ░░░░░░░     ░░░░░░░
     │                          ███████     ███████     ░░░░░░░
     │                          ███████     ███████     ░░░░░░░
     │                          ███████     ███████     ░░░░░░░       252
 240 ┤                          ███████     ███████     ░░░░░░░     ░░░░░░░
     │                          ███████     ███████     ░░░░░░░     ░░░░░░░
     │                153       ███████     ███████     ███████     ░░░░░░░
     │              ░░░░░░░     ███████     ███████     ███████     ░░░░░░░
 120 ┤              ███████     ███████     ███████     ███████     ░░░░░░░       103
     │              ███████     ███████     ███████     ███████     ░░░░░░░     ░░░░░░░
     │     36       ███████     ███████     ███████     ███████     ░░░░░░░     ░░░░░░░        21          4
     │  ███████     ███████     ███████     ███████     ███████     ███████     ░░░░░░░     ░░░░░░░     ░░░░░░░
   0 └────────────────────────────────────────────────────────────────────────────────────────────────────────────
       3.0 to 3.1  3.1 to 3.2  3.2 to 3.3  3.3 to 3.4  3.4 to 3.5  3.5 to 3.6  3.6 to 3.7  3.7 to 3.8  3.8 to 3.9

```

The series of the legend share the same bins, because the bins come from the calculated table, and the count of each shift follows the legend filter.