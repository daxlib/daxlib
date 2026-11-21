# EG.LikeForLike

Function for **like-for-like comparisons** (same-store sales): a value computed only over the entities that were active in all the periods being compared, so that stores, products or customers that appeared or disappeared in between do not distort the growth. No status table and no extra relationship are needed, and it works for any entity and any period grain.

## What it does

`EG.LikeForLike.Compute` keeps only the entities that are active in **every period selected in the report** and, when a comparison filter is given, also in **every period of the comparison**. An entity is active in a period when `<existsExpr>` is not blank for it in that period.

**Activity is a fixed attribute of the entity:** it is measured over the whole period and ignores the filters of the report. A slicer used to see detail (a product category, a customer gender, a partial date range) changes the values shown, but never which entities are comparable.

| Report | `<comparisonFilter>` | Comparable entities |
| --- | --- | --- |
| Matrix with the years 2018, 2019 and 2020 on the columns | none | Active in 2018, 2019 and 2020 |
| Card with 2019 selected | `SAMEPERIODLASTYEAR ( 'Date'[Date] )` | Active in 2019 and 2018 |
| Matrix by year, 2018 to 2020, growth against the year before | `SAMEPERIODLASTYEAR ( 'Date'[Date] )` | Active in 2017, 2018, 2019 and 2020 |
| Card with June 2019 selected, month grain | `PREVIOUSMONTH ( 'Date'[Date] )` | Active in June and May 2019 |

The same set of entities is used in every cell of the visual, so the columns of a matrix and its total are comparable with each other.

1. **Selected periods:** the periods of `<periodColumn>` in the dates selected in slicers and filters, not only the period of the current cell.
2. **Activity is measured over whole periods of `<periodColumn>`.** With January to June selected and `'Date'[Year]`, a store must be active at any moment of each year. For a stricter test, use a finer grain, such as `'Date'[Year Month Number]`.
3. **Periods with no activity at all are ignored.** A future month with no data, or the comparison period of the first year with data, does not disqualify anyone. In that first year, `<returnComparison>` returns `BLANK()`.
4. **`<returnComparison>`:** when `TRUE`, returns the value of the comparison period of each cell (the same month last year, for example) for the same entities, which is what the growth calculation needs.

> **Note:** With a slicer on a product category or a customer gender, the comparable stores are the same as without the slicer. To measure activity on a subset, put the condition inside `<existsExpr>`, e.g., `CALCULATE ( [Sales Amount], 'Product'[Category] = "Audio" )`. The values shown still respect every filter, including filters on the entity itself (a country slicer, the store on the rows).

---

## Function

```DAX
EG.LikeForLike.Compute (
    valueExpr,          -- value to compute, e.g., [Sales Amount]
    entityColumn,       -- column that identifies the entity, e.g., Store[StoreKey]
    periodColumn,       -- column of the date table with the periods, e.g., 'Date'[Year]
    existsExpr,         -- value that tells whether the entity is active, e.g., [Sales Amount]
    [comparisonFilter], -- (optional) filter of the comparison period, e.g., SAMEPERIODLASTYEAR ( 'Date'[Date] )
    [returnComparison]  -- (optional) TRUE returns the value of the comparison period (default: FALSE)
)
```

**Return:** `<valueExpr>` over the comparable entities (or its value in the comparison period, when `<returnComparison>` is `TRUE`), or `BLANK()` when no entity is comparable.

---

## Basic usage

### Same stores across the selected years

Matrix with `'Date'[Year]` on the columns and a slicer on the years 2018, 2019 and 2020:

```DAX
Same Store Sales =
EG.LikeForLike.Compute (
    [Sales Amount],
    Store[StoreKey],
    'Date'[Year],
    [Sales Amount]
)
```

### Result

| | 2018 | 2019 | 2020 | Total |
| --- | ---: | ---: | ---: | ---: |
| Stores with sales | 62 | 61 | 58 | |
| `[Sales Amount]` | 1,798,092,764.88 | 1,287,977,537.59 | 278,190,232.03 | 3,364,260,534.51 |
| `Same Store Sales` | 1,739,156,678.81 | 1,264,442,321.87 | 276,565,121.09 | 3,280,164,121.78 |

Every column of `Same Store Sales` uses the same 57 stores, the ones with sales in all three years.

---

## Growth against the previous year

```DAX
LFL Sales =
EG.LikeForLike.Compute (
    [Sales Amount],
    Store[StoreKey],
    'Date'[Year],
    [Sales Amount],
    SAMEPERIODLASTYEAR ( 'Date'[Date] )
)
```

```DAX
LFL Sales PY =
EG.LikeForLike.Compute (
    [Sales Amount],
    Store[StoreKey],
    'Date'[Year],
    [Sales Amount],
    SAMEPERIODLASTYEAR ( 'Date'[Date] ),
    TRUE ()
)

```

```DAX
Comparable Stores =
EG.LikeForLike.Compute (
    DISTINCTCOUNT ( Store[StoreKey] ),
    Store[StoreKey],
    'Date'[Year],
    [Sales Amount],
    SAMEPERIODLASTYEAR ( 'Date'[Date] )
)
```

### Result

With the year 2019 selected:

| Measure | Value |
| --- | ---: |
| `[Sales Amount]` 2019 | 1,287,977,537.59 |
| `[Sales Amount]` 2018 | 1,798,092,764.88 |
| `Comparable Stores` | 60 |
| `LFL Sales` | 1,283,115,962.92 |
| `LFL Sales PY` | 1,788,263,992.71 |

The same measures work in a matrix by month: each month is compared with the same month of the previous year, always for the 60 stores active in both years.

Get the comparison value with `<returnComparison>`, as in `LFL Sales PY`, and not with `CALCULATE ( [LFL Sales], SAMEPERIODLASTYEAR ( 'Date'[Date] ) )`: moving the dates outside the function also moves the selected periods, so 2018 would be compared with 2017 instead of 2019 with 2018.

---

## Related reading

Without `<comparisonFilter>`, the function follows the rule of the [like-for-like pattern](https://www.daxpatterns.com/like-for-like-comparison/) and of the SQLBI article [Creating functions for the like-for-like DAX pattern](https://www.sqlbi.com/articles/creating-functions-for-the-like-for-like-dax-pattern/): only the entities active in all the selected periods.
