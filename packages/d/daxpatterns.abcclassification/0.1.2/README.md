# DaxPatterns.AbcClassification

Functions to implement the [ABC Classification](https://www.daxpatterns.com/abc-classification/) pattern (dynamic version) from [DAX Patterns](https://www.daxpatterns.com/).

## What it does

`ComputeInAbcClass` evaluates an expression by filtering the *entity* (e.g., Product, Customer) belongs to the ABC classes active in the filter context from  a disconnected “ABC Classes” table, **respecting all active report filters**. Use it to build dynamic ABC analyses that slice by time, region, or any other dimension. ([daxpatterns.com][1])

---

## Function

```DAX
ComputeInAbcClass (
    <valueExpr>,               -- e.g., [Sales Amount] (must be additive, SUM-based)
    <transactionsTable>,       -- e.g., Sales
    <itemTable>,               -- e.g., 'Product'
    <itemKeyColumn>,           -- e.g., 'Product'[ProductKey]
    <abcClassTable>,           -- e.g., 'ABC Classes' (disconnected)
    <abcLowerBoundaryColumn>,  -- e.g., 'ABC Classes'[Lower Boundary]  -- inclusive
    <abcUpperBoundaryColumn]   -- e.g., 'ABC Classes'[Upper Boundary]  -- exclusive (see notes)
)
```

**Return:** `TRUE` if the entity’s cumulative share (by `<valueExpr>`) falls within the class boundaries on the current row of `<abcClassTable>`. Otherwise `FALSE`. Use it inside measures to filter or label entities.

**Assumptions:** Dynamic ABC as in the DAX Patterns article with a parameter table of class boundaries. The measure supplied must aggregate with `SUM`. ([daxpatterns.com][1])

---

## Model requirements

1. **Value measure:** Additive, e.g.:

   ```DAX
   Sales Amount = 
   SUMX ( Sales, Sales[Quantity] * Sales[Net Price] )
   ```
2. **Entity table:** The table that lists the items to classify (e.g., `Product`).
3. **Classes table (disconnected):** No relationships. Contains at least:

   * `Class` (A/B/C or any label)
   * `Lower Boundary` (0–1)
   * `Upper Boundary` (0–1)

Example `ABC Classes` (typical Pareto configuration):

```DAX
ABC Classes = 
DATATABLE (
    "Class", STRING,
    "Lower Boundary", DOUBLE,
    "Upper Boundary", DOUBLE,
{
    { "A", 0.00, 0.70 },
    { "B", 0.70, 0.90 },
    { "C", 0.90, 1.00 }
})
```

This matches the pattern described in DAX Patterns and its dynamic variant (classification driven by a disconnected parameter table). ([daxpatterns.com][1])

---

## Basic usage

```DAX
ABC Sales Amount =
ComputeInAbcClass (
    [Sales Amount],
    Sales,
    'Product',
    'Product'[ProductKey],
    'ABC Classes',
    'ABC Classes'[Lower Boundary], 
    'ABC Classes'[Upper Boundary]
)

```

These produce a **dynamic** ABC where class membership updates with filters (e.g., year, region). ([daxpatterns.com][1])

---

## Tips, constraints, and performance

* **Additivity:** `<valueExpr>` must be SUM-based for correct cumulative ranking in the dynamic pattern. Non-additive measures break the logic. ([daxpatterns.com][1])
* **Boundaries:** Treat `Lower Boundary` as inclusive and `Upper Boundary` as exclusive to avoid overlaps at exact cut points (e.g., 0.70 belongs to B if A ends at 0.70).
* **Disconnected classes:** Do **not** relate `ABC Classes` to other tables. It is a parameter table used by the measure. ([daxpatterns.com][2])
* **Granularity:** Pass the **entity key** that defines classification granularity.
* **Scaling:** With very high entity cardinality, consider pre-aggregation by entity or snapshot ABC when dynamics are not needed. ([daxpatterns.com][1])

---

## Related reading

* DAX Patterns — ABC classification (static, snapshot, dynamic). ([daxpatterns.com][1])
* DAX Patterns — Dynamic ABC Classification (concepts and step-by-step). ([daxpatterns.com][1])
* Parameter Table pattern. ([daxpatterns.com][2])
* DAX Lib package list (package reference). ([daxlib.org][3])

---

## License

MIT License.

[1]: https://www.daxpatterns.com/abc-classification/ "ABC classification"
[2]: https://www.daxpatterns.com/parameter-table/ "Parameter table"
[3]: https://daxlib.org/packages/ "Packages - Extend Power BI with DAX Lib"
