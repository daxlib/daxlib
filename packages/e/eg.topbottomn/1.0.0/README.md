# EG.TopBottomN

Function to build a **TopN + BottomN table** in Power BI with a single measure pattern: the top N items, two `⋮` placeholder rows, and the bottom N items, all in the same visual and **without any visual-level filter**. One function handles the ranking, the row visibility, the placeholders and the rank number itself.

## What it does

`EG.TopBottomN.Display` ranks the items of an entity (customers, products, suppliers, ...) by a measure and, for the current row of the visual, returns:

| Descending rank of the row | Returned value |
| --- | --- |
| `1` to `N` (TopN) | `<valueExpr>` |
| `N + 1` and `N + 2` | `<placeholder>` (default `⋮`) |
| last `N` (BottomN) | `<valueExpr>` |
| any other rank | `BLANK()` |

Because every other item returns `BLANK()`, Power BI hides those rows automatically: the visual shows only the TopN, the placeholders and the BottomN.

When `<returnRank>` is `TRUE`, the function returns the descending rank of every displayed row instead of `<valueExpr>`, so the same function also feeds the rank column.

> **Note:** The rank is computed over the items visible after removing **only** the filter on `<entityKey>`. Slicers on other columns of the entity table (e.g., `Customer[Country]`) are respected, so the TopN/BottomN is computed inside the current selection. Items where `<rankExpr>` is blank are not ranked and return `BLANK()`. Ties are broken by `<entityKey>` in ascending order, so ranks are always unique.

---

## Function

```DAX
EG.TopBottomN.Display (
    entityKey,          -- key column of the entity, e.g., Customer[CustomerKey]
    rankExpr,           -- expression used to rank, e.g., [Sales Amount]
    valueExpr,          -- value to display on visible rows, e.g., [Sales Amount]
    [topBottomN],       -- (optional) number of items on top and on bottom (default: 10)
    [placeholder],      -- (optional) value shown on rows N+1 and N+2 (default: UNICHAR ( 8942 ) = "⋮")
    [returnRank]        -- (optional) TRUE returns the rank instead of valueExpr (default: FALSE)
)
```

**Return:** `<valueExpr>`, `<placeholder>`, the descending rank (when `<returnRank>` is `TRUE`) or `BLANK()`, according to the table above.

---

## Model requirements

### `<entityKey>` — the rows of the visual

The key column of the entity must be the **only column** of that entity table on the rows of the visual. Show other attributes (name, category, ...) through measures that also use the function (see `Customer Name` below), not as columns: a second column of the same table on the rows keeps its own filter and breaks the ranking.

### Every value in the visual must use the function

Power BI hides a row only when **all** measures of that row are blank. Any measure placed in the visual without `EG.TopBottomN.Display` keeps every item visible.

### Selecting N (optional)

To let report users choose N, create a numeric range parameter (Modeling > New parameter > Numeric range) and pass its selected value to `<topBottomN>`:

```DAX
ValueTopN = GENERATESERIES ( 1, 15, 1 )    -- the parameter wizard names the column ValueTopN[ValueTopN]
```

### Totals

The function returns `BLANK()` on total rows. Turn off **Totals** in the table visual.

---

## Basic usage

The example below builds a table visual with `Customer[CustomerKey]` on the rows and the following measures.

```DAX
Customer Name =
EG.TopBottomN.Display (
    Customer[CustomerKey],
    [Sales Amount],
    SELECTEDVALUE ( Customer[Name] ),
    SELECTEDVALUE ( ValueTopN[ValueTopN] )
)
```

```DAX
Rank Customer Desc =
EG.TopBottomN.Display (
    Customer[CustomerKey],
    [Sales Amount],
    BLANK (),
    SELECTEDVALUE ( ValueTopN[ValueTopN] ),
    BLANK (),
    TRUE ()
)
```

```DAX
Sales Amount Customer =
EG.TopBottomN.Display (
    Customer[CustomerKey],
    [Sales Amount],
    [Sales Amount],
    SELECTEDVALUE ( ValueTopN[ValueTopN] )
)
```

```DAX
Sales Amount Perc by Customer =
EG.TopBottomN.Display (
    Customer[CustomerKey],
    [Sales Amount],
    DIVIDE ( [Sales Amount], CALCULATE ( [Sales Amount], ALL ( Customer ) ) ),
    SELECTEDVALUE ( ValueTopN[ValueTopN] ),
    BLANK ()    -- no placeholder: the percentage stays empty on rows N+1 and N+2
)
```

Sort the visual by `Rank Customer Desc` in **ascending** order.

### Result

With `topBottomN = 3`, 200 customers with sales, a total `[Sales Amount]` of 1,000,000.00 and the conditional formatting of the next section applied, the visual shows (with the measures renamed in the visual):

| CustomerKey | Name | Rank | Sales Amount | Sales Amount Perc |
| ---: | --- | ---: | ---: | ---: |
| 1042 | Ana Souza | 1 | 48,360.00 | 4.84% |
| 2187 | Bruno Lima | 2 | 41,920.00 | 4.19% |
| 1503 | Carla Mendes | 3 | 39,775.00 | 3.98% |
| | ⋮ | | ⋮ | |
| | ⋮ | | ⋮ | |
| 2954 | Diego Rocha | 198 | 312.00 | 0.03% |
| 1129 | Elisa Prado | 199 | 205.00 | 0.02% |
| 3407 | Fábio Nunes | 200 | 118.00 | 0.01% |

Ranks 1 to 3, the two placeholder rows (ranks 4 and 5) and ranks 198 to 200. The 192 customers ranked 6 to 197 return `BLANK()` in every measure, so Power BI hides their rows.

---

## Conditional formatting: hiding the placeholder rows

On the placeholder rows, the `CustomerKey` column and the `Rank Customer Desc` measure still show the key and the rank of the items at positions N+1 and N+2. To hide them, the same function generates the font color:

```DAX
Color Values Customer =
VAR _Color =
    EG.TopBottomN.Display (
        Customer[CustomerKey],
        [Sales Amount],
        BLANK (),
        SELECTEDVALUE ( ValueTopN[ValueTopN] ),
        "#00000000"
    )
RETURN
    CONVERT ( _Color, STRING )
```

Apply it in **Conditional formatting > Font color > Format style: Field value** on the `CustomerKey` column and on the `Rank Customer Desc` measure.

This measure has two peculiarities:

1. **The color is passed as `<placeholder>`.** `"#00000000"` is a fully transparent color (`#RRGGBBAA` with alpha `00`). The function returns it only on rows N+1 and N+2 and returns `BLANK()` everywhere else, because `<valueExpr>` is `BLANK()`. On blank rows Power BI keeps the default font color of the theme. The text on the placeholder rows becomes invisible regardless of the theme, the row banding or the table style, and there is no ranking logic to repeat in the formatting measure.

2. **`CONVERT ( _Color, STRING )` is required.** The function can return a number, a text or any value passed to `<valueExpr>` and `<placeholder>`, so a measure that calls it is typed as **Variant**. The **Field value** option of conditional formatting only accepts measures of type **Text**. Without `CONVERT` the measure is not listed, or an existing rule stops being applied. `CONVERT` makes the measure a Text measure and keeps `BLANK()` as blank on the other rows.
