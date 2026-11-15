# EG.Cohort

Function for **cohort analysis**: groups customers by the period of their **first order** and evaluates any measure for that group N periods later — active customers, retention, sales, average ticket, lifetime value (LTV).

No calculated column is needed. The first order of each customer is found at query time, so:

- the grain is free: month, quarter, week or any other period, just by changing one argument;
- the cohort respects the filters of the report (see [Dynamic cohort](#dynamic-cohort)).

## What it does

`EG.Cohort.Compute` evaluates `<valueExpr>` for the customers of the cohort in the requested period:

1. **First order of every customer**, in a single query, ignoring only the date filters.
2. **New customers of the visual:** only those whose first order falls within the periods shown by the visual (`ALLSELECTED`) move on.
3. **Cohort customers:** those whose first order falls between the first and the last order date of the period of the cell.
4. **Cohort table:** for those customers, the value of `<valueExpr>` in each period. The engine builds this table once per cohort, not once per cell; each cell only looks up its row.
5. **Subtotals and lifetime** are calculated directly, without the table.

> **Note:** The cohort of each cell comes from the date filter of the visual: it is the period (or periods) of `<periodColumn>` on the rows of the matrix.

---

## Function

```DAX
EG.Cohort.Compute (
    valueExpr,              -- metric evaluated for the cohort, e.g., [Sales Amount]
    customerColumn,         -- customer column in the fact table, e.g., Sales[CustomerKey]
    orderDateColumn,        -- order date column in the fact table, e.g., Sales[Order Date]
    periodColumn,           -- consecutive integer period column, e.g., 'Date'[Year Month Number]
    [periodsAfter],         -- (optional) periods after the first order; BLANK () is the whole lifetime (default)
    [shareOfFirstPeriod]    -- (optional) TRUE () returns the value divided by period 0 (default: FALSE ())
)
```

| Parameter | Required | Default | Description |
| --- | --- | --- | --- |
| `valueExpr` | Yes | — | Metric evaluated for the cohort: a measure or expression such as `[Sales Amount]` or `DISTINCTCOUNT ( Sales[CustomerKey] )`. It can be non-additive (average ticket, distinct count). |
| `customerColumn` | Yes | — | Column that identifies the customer **in the fact table**. The cohort is formed by it. |
| `orderDateColumn` | Yes | — | Order date column **in the fact table**. The first order is found by it. |
| `periodColumn` | Yes | — | Period column of the date table. Defines the grain of the analysis. See [Period column](#period-column). |
| `periodsAfter` | No | `BLANK ()` | Periods after the first order. `0` is the cohort period itself. Blank evaluates the **whole lifetime** of the cohort, from the first order onwards. |
| `shareOfFirstPeriod` | No | `FALSE ()` | `TRUE ()` returns the value divided by the value of period 0. With a customer count, this is the **retention**. |

**Return:** the value of `<valueExpr>` for the cohort customers in the requested period, or `BLANK()` when there is no data.

---

## Model requirements

- A date table related to the fact table by the order date (active relationship).
- In the date table, a **period column**: an integer in which +1 is the next period. See [Period column](#period-column).
- A **disconnected offsets table**, with no relationship to anything:

  ```DAX
  MonthsAfter = GENERATESERIES ( 0, 24, 1 )
  ```

  It works for any grain: the value means "periods after the first order".

---

## Basic usage

The examples use Contoso: `Sales[CustomerKey]`, `Sales[Order Date]`, `'Date'[Year Month Number]` and the `MonthsAfter` table.

### Active customers N months later

```DAX
Cohort Customers =
EG.Cohort.Compute (
    DISTINCTCOUNT ( Sales[CustomerKey] ),
    Sales[CustomerKey],
    Sales[Order Date],
    'Date'[Year Month Number],
    SELECTEDVALUE ( MonthsAfter[Value] )
)
```

Column 0 shows the size of the cohort; the next ones show how many of those customers bought again.

### Retention (%)

```DAX
Cohort Retention =
EG.Cohort.Compute (
    DISTINCTCOUNT ( Sales[CustomerKey] ),
    Sales[CustomerKey],
    Sales[Order Date],
    'Date'[Year Month Number],
    SELECTEDVALUE ( MonthsAfter[Value] ),
    TRUE ()
)
```

Use `shareOfFirstPeriod` instead of dividing two calls of the function: in a single call the work is done once (2.2 s against 3.8 s in the test).

### Sales of the cohort N months later

```DAX
Cohort Sales Amount =
EG.Cohort.Compute (
    [Sales Amount],
    Sales[CustomerKey],
    Sales[Order Date],
    'Date'[Year Month Number],
    SELECTEDVALUE ( MonthsAfter[Value] )
)
```

### Sales relative to the first month (%)

```DAX
Cohort Sales vs First Month =
EG.Cohort.Compute (
    [Sales Amount],
    Sales[CustomerKey],
    Sales[Order Date],
    'Date'[Year Month Number],
    SELECTEDVALUE ( MonthsAfter[Value] ),
    TRUE ()
)
```

### Cohort LTV (whole lifetime)

Without `periodsAfter`, the function adds up everything the cohort bought since the first order:

```DAX
Cohort LTV =
EG.Cohort.Compute (
    [Sales Amount],
    Sales[CustomerKey],
    Sales[Order Date],
    'Date'[Year Month Number]
)
```

In a matrix with `MonthsAfter[Value]` on the columns, the total column of `Cohort Sales Amount` already shows this value, because `SELECTEDVALUE` returns blank there.

### Average sales per customer (non-additive metric)

```DAX
Cohort Avg Sales per Customer =
EG.Cohort.Compute (
    DIVIDE ( [Sales Amount], DISTINCTCOUNT ( Sales[CustomerKey] ) ),
    Sales[CustomerKey],
    Sales[Order Date],
    'Date'[Year Month Number],
    SELECTEDVALUE ( MonthsAfter[Value] )
)
```

### Other grains: week and quarter

Only the period column changes. The same `MonthsAfter` table now means "weeks after" or "quarters after":

```DAX
Cohort Retention Weekly =
EG.Cohort.Compute (
    DISTINCTCOUNT ( Sales[CustomerKey] ),
    Sales[CustomerKey],
    Sales[Order Date],
    'Date'[Week Index],
    SELECTEDVALUE ( MonthsAfter[Value] ),
    TRUE ()
)
```

```DAX
Cohort Customers Quarterly =
EG.Cohort.Compute (
    DISTINCTCOUNT ( Sales[CustomerKey] ),
    Sales[CustomerKey],
    Sales[Order Date],
    'Date'[Year Quarter Number],
    SELECTEDVALUE ( MonthsAfter[Value] )
)
```

On the rows of the matrix, use the period column itself or a label column of the same grain.

---

## Building the visual

1. **Matrix.**
2. **Rows:** the cohort period, at the same grain as `<periodColumn>`. For months, `'Date'[Year Month]` (sorted by `'Date'[Year Month Number]`).
3. **Columns:** `MonthsAfter[Value]`.
4. **Values:** one of the measures above.
5. **Visual filter:** limit the cohorts, e.g., `'Date'[Year]` to two years. See [Performance](#performance).
6. **Conditional formatting:** background color scale on the retention measure.

What to check:

- With `shareOfFirstPeriod`, column 0 is 100% in every cohort.
- Cells after the last date with sales are blank, forming the triangle.

---

## Period column

`<periodColumn>` must be a consecutive integer, because the function adds `<periodsAfter>` to it.

| Grain | Column | Expression |
| --- | --- | --- |
| Month | `'Date'[Year Month Number]` | `YEAR ( 'Date'[Date] ) * 12 + MONTH ( 'Date'[Date] )` |
| Quarter | `'Date'[Year Quarter Number]` | `YEAR ( 'Date'[Date] ) * 4 + QUARTER ( 'Date'[Date] )` |
| Week | `'Date'[Week Index]` | `INT ( ( 'Date'[Date] - DATE ( 2000, 1, 3 ) ) / 7 )` (weeks starting on Monday) |

A code such as `202412` **does not work**: after December it jumps to `202501`.

---

## Dynamic cohort

The first order is calculated ignoring **only the date filters**. Every other filter still applies:

- **Customer attribute** filter (country, segment): only restricts which customers enter. The cohort of each one does not change.
- **Product, store or channel** filter: redefines the cohort. With brand X filtered, the cohort of a customer is the period of their first order **of brand X**.

If the cohort must be fixed (the first order of the customer in any product), the function does not fit; that is the case for a calculated column in the customer table.

> **Note:** Filter the visual by whole periods of the same grain. With weeks, a year filter cuts the first and last week, and those cohorts only include the first orders on the visible days

## Subtotals

- **Subtotal or total row** (several cohort periods): the cohort becomes "first order in any of these periods", and the whole range is shifted by `periodsAfter`.
- **Total column** (`periodsAfter` blank): whole lifetime of the cohort.

---

## Limitations

- **Very large matrices** (more than a hundred cohorts) take over 10 s.
