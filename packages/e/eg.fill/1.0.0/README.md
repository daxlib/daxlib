# EG.Fill

Function to **fill down** and **fill up** in a measure: on rows of the visual where a value is blank, it returns the value of the nearest non-blank row before (fill down) or after (fill up) it, in the order of a sort column.

The value can be **a measure** (`[Sales Amount]`) or **a column** through an aggregation (`MAX ( Sales[Region] )`). It is the report-side equivalent of Power Query's *Fill → Down / Up*, without a calculated column and respecting the filters of the report.

| Index | Region | Fill down (`0`) | Fill up (`1`) |
| --- | --- | --- | --- |
| 1 | | | North |
| 2 | North | North | North |
| 3 | | North | South |
| 4 | | North | South |
| 5 | South | South | South |
| 6 | | South | |

---

## Function

```DAX
EG.Fill.Compute (
    valueExpr,          -- value to fill, e.g., [Sales Amount] or MAX ( Sales[Region] )
    orderColumn,        -- column that defines the order, e.g., 'Date'[Date] or Sales[Index]
    [direction]         -- (optional) 0 or DESC: fill down (default); 1 or ASC: fill up
)
```

| Parameter | Required | Default | Values | Description |
| --- | --- | --- | --- | --- |
| `valueExpr` | Yes | — | A measure, or a column through `MAX`, `MIN` or `SELECTEDVALUE` | Value to fill. Text or number. |
| `orderColumn` | Yes | — | A date, a numeric month, an index | Defines the order. Must be on the rows of the visual. |
| `direction` | No | `0` | `0` or `DESC`, `1` or `ASC` | Fill down copies the last non-blank value up to the row (inclusive). Fill up copies the first non-blank value from the row onwards. |

**Return:** the value of the row when it is not blank; otherwise, the copied value. `BLANK()`:

- with fill down, before the first value;
- with fill up, after the last value;
- on total rows.

---

## Model requirements

### `<orderColumn>` — on the rows of the visual

The sort column must be on the rows of the visual, with one value per row of the visual: a date (`'Date'[Date]`), a numeric month (`'Date'[Year Month Number]`) or an index. It can be in a dimension, such as the Date table. Without it on the rows, the function returns `BLANK()`.

### Only `BLANK` counts as empty

Zero and empty text (`""`) are values. To treat zero as empty, pass `IF ( [Measure] <> 0, [Measure] )`.

---

## Basic usage

Measure, per day: days without sales show the sales of the last day with sales.

```DAX
Sales Last Day = EG.Fill.Compute ( [Sales Amount], 'Date'[Date] )
```

Measure, fill up: days without sales show the sales of the next day with sales.

```DAX
Sales Next Day = EG.Fill.Compute ( [Sales Amount], 'Date'[Date], ASC )
```

Text column, through `SELECTEDVALUE`:

```DAX
Region Fill Down = EG.Fill.Compute ( SELECTEDVALUE ( Sales[Region] ), Sales[Index] )
```

Numeric column, fill up:

```DAX
Amount Fill Up = EG.Fill.Compute ( SELECTEDVALUE ( Sales[Amount] ), Sales[Index], ASC )
```

### Building the visual

1. **Table** with the sort column on the rows, e.g., `'Date'[Date]`.
2. **Values:** the original measure and the fill measure.
3. **Per group:** put the group column before the sort column on the rows, e.g., `Store[StoreKey]` and then `'Date'[Date]`. The fill stays inside each store and does not cross from one store to the next.
