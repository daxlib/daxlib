# EG.TopNOthers

Function to show the **top N items plus one "Others" row** that groups everything else, in the same visual, with a dynamic N and respecting slicers. The "Others" row is computed over the remaining items as a set, so it stays correct for non-additive measures such as distinct counts. The same function returns the position of each row, to sort the visual with "Others" last.

## What it does

`EG.TopNOthers.Compute` ranks the items of a column by a measure and, for the current row of the visual, returns:

| Row of the visual | Returned value | With `<returnPosition>` = `TRUE` |
| --- | --- | --- |
| An item in the top N | `<valueExpr>` of that item | 1 to N |
| The "Others" row | `<valueExpr>` of all the remaining items together | N + 1 |
| Any other item | `BLANK()` | `BLANK()` |
| Total | `<valueExpr>`, unchanged | `BLANK()` |

Because the items outside the top N return `BLANK()`, Power BI hides their rows: the visual shows N rows plus "Others", and the total still matches the model.

1. **Candidates:** the items of `<itemColumn>` visible in the current filter context with a non-blank `<valueExpr>`.
2. **Ties at the cut:** broken by the item in ascending order, so the result always has exactly N items.
3. **Position of tied items:** items with the same value share a position, and the next position is skipped (9, 9, then 11 for "Others").
4. **"Others" disappears** when no item is left outside the top N.

> **Note:** Slicers on the item table (brand, category) and on any other table are respected, so the top N is computed inside the current selection. The total row returns `<valueExpr>` unchanged: it is not restricted to the helper values selected in a slicer.

---

## Function

```DAX
EG.TopNOthers.Compute (
    valueExpr,          -- value to rank and return, e.g., [Sales Amount]
    itemColumn,         -- real item column, e.g., 'Product'[Product Name]
    helperColumn,       -- column of the disconnected helper table, e.g., 'Product Names'[Product Name]
    n,                  -- number of top items
    [othersLabel],      -- (optional) label of the row that groups the rest (default: "Others")
    [sortOrder],        -- (optional) DESC or 0: top N (default); ASC or 1: bottom N
    [returnPosition]    -- (optional) TRUE returns the position of the row instead of the value (default: FALSE)
)
```

**Return:** `<valueExpr>` of the item, `<valueExpr>` of the remaining items on the "Others" row, the position (when `<returnPosition>` is `TRUE`) or `BLANK()`, according to the table above.

---

## Model requirements

### `<helperColumn>` — a disconnected helper table

An "Others" row does not exist in the model, so the visual needs a **disconnected helper table** with the item names plus the "Others" label. Create it once as a calculated table:

```DAX
Product Names =
UNION (
    DISTINCT ( 'Product'[Product Name] ),
    { "Others" }
)
```

- Do **not** create a relationship between the helper table and the model.
- Put the helper column (`'Product Names'[Product Name]`) on the rows of the visual, not the real column.
- The label in the helper table must be the same text passed to `<othersLabel>`. If a real item has the same name as the label, choose another label.

### Every value in the visual must use the function

Power BI hides a row only when **all** measures of that row are blank. Any measure placed in the visual without `EG.TopNOthers.Compute` keeps every item visible.

### Selecting N (optional)

To let report users choose N, create a numeric range parameter (Modeling > New parameter > Numeric range) and pass its selected value to `<n>`:

```DAX
Top N = GENERATESERIES ( 1, 20, 1 )    -- the parameter wizard names the column 'Top N'[Top N]
```

---

## Basic usage

Table visual with `'Product Names'[Product Name]` on the rows:

```DAX
Sales Top 10 =
EG.TopNOthers.Compute (
    [Sales Amount],
    'Product'[Product Name],
    'Product Names'[Product Name],
    10
)
```

```DAX
Sales Top 10 Position =
EG.TopNOthers.Compute (
    [Sales Amount],
    'Product'[Product Name],
    'Product Names'[Product Name],
    10,
    "Others",
    DESC,
    TRUE ()
)
```

Sort the visual by `Sales Top 10 Position` in **ascending** order. In a chart, add the position measure to the tooltips and sort the axis by it.

```DAX
Sales Top N =
EG.TopNOthers.Compute (
    [Sales Amount],
    'Product'[Product Name],
    'Product Names'[Product Name],
    SELECTEDVALUE ( 'Top N'[Top N], 10 )
)
```

```DAX
Sales Bottom 10 =
EG.TopNOthers.Compute (
    [Sales Amount],
    'Product'[Product Name],
    'Product Names'[Product Name],
    10,
    "Others",
    ASC
)
```

---

## Non-additive measures: distinct customers

The "Others" row evaluates `<valueExpr>` over all the remaining items at once, so distinct counts and ratios stay correct:

```DAX
Customers Top 10 =
EG.TopNOthers.Compute (
    DISTINCTCOUNT ( Sales[CustomerKey] ),
    'Product'[Product Name],
    'Product Names'[Product Name],
    10
)
```

### Result

Year 2019, `Customers Top 10` (distinct customers by product) and its position:

| Product Name | Position | Customers |
| --- | ---: | ---: |
| Contoso DVD 60 DVD Storage Binder L20 Silver | 1 | 3,204 |
| SV DVD External DVD Burner M200 Grey | 2 | 3,190 |
| SV DVD 14-Inch Player Portable L100 Silver | 3 | 3,188 |
| ... | ... | ... |
| Contoso DVD 60 DVD Storage Binder L20 Red | 9 | 3,146 |
| SV DVD 7-Inch Player Portable E200 Black | 9 | 3,146 |
| Others | 11 | 513,050 |

"Others" is the number of distinct customers that bought any product outside the top 10. It is not the sum of the other rows: a customer that bought two of those products counts once. Positions 9 and 10 have the same value; with `n = 9`, only *Contoso DVD 60 DVD Storage Binder L20 Red* stays, because it comes first alphabetically.

---

## Notes

- Hierarchies with more than one level and an "Others" row per parent group are out of scope.
