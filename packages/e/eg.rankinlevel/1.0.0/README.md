# EG.RankInLevel

Function for Power BI **visual calculations** that ranks each row among its **siblings** — the rows of the same level under the same parent — ordered by a value, **without referencing any axis column**.

Because it does not depend on column names, the same calculation stays correct when the axis changes: field parameters, drill-down, expand/collapse or a different hierarchy.

## What it does

In visual calculations, `RANK` and `ROWNUMBER` do not accept `ORDERBY` together with a reset (`LOWESTPARENT`, `HIGHESTPARENT`):

> The RESET parameter cannot be used in combination with ORDERBY or PARTITIONBY.

That leaves two bad options: rank by the order of the axis (not by the value), or use `PARTITIONBY ( [Column] )`, which breaks when the column leaves the visual.

`EG.RankInLevel.Compute` can be used with field parameters or customized visuals, and it works with any new column that appears.

> **Note:** The function works **only in visual calculations**. It depends on the axes of the visual (`ROWS`, `COLUMNS`) and cannot be used in measures.

---

## Function

```DAX
EG.RankInLevel.Compute (
    valueExpr,          -- value used to order the rank, e.g., [Sales Amount]
    ties,               -- how to number ties: SKIP or DENSE
    [sortOrder],        -- (optional) DESC or 0 (default), ASC or 1
    [breakTies],        -- (optional) TRUE () returns unique positions; ties is then ignored (default: FALSE ())
    [visualAxis],       -- (optional) axis holding the hierarchy to rank: ROWS (default) or COLUMNS
    [resetLevel]        -- (optional) group in which the rank restarts: LOWESTPARENT (default), HIGHESTPARENT or NONE
)
```

| Parameter | Required | Default | Values | Description |
| --- | --- | --- | --- | --- |
| `valueExpr` | Yes | — | A field of the visual, e.g., `[Sales Amount]` | Value used to order the rank. |
| `ties` | Yes | — | `SKIP`, `DENSE` | How to number ties. See [Ties](#ties). |
| `sortOrder` | No | `0` (descending) | `DESC` or `0`, `ASC` or `1` | Descending: the highest value is 1st. Ascending: the lowest value is 1st. |
| `breakTies` | No | `FALSE ()` | `TRUE ()`, `FALSE ()` | `TRUE ()` breaks ties: no position repeats. `ties` is then ignored. |
| `visualAxis` | No | `ROWS` | `ROWS`, `COLUMNS` | Axis of the visual holding the hierarchy to rank. |
| `resetLevel` | No | `LOWESTPARENT` | `LOWESTPARENT`, `HIGHESTPARENT`, `NONE` | Group in which the rank restarts. See [Where the rank restarts](#where-the-rank-restarts). |

**Return:** an integer position, or `BLANK()` when `<valueExpr>` is blank.

---

## Basic usage

Rank among siblings, from highest to lowest:

```DAX
Rank Sales = EG.RankInLevel.Compute ( [Sales Amount], SKIP )
```

Dense rank, from lowest to highest:

```DAX
Rank Sales = EG.RankInLevel.Compute ( [Sales Amount], DENSE, ASC )
```

No repeated positions:

```DAX
Rank Sales = EG.RankInLevel.Compute ( [Sales Amount], SKIP, DESC, TRUE () )
```

Hierarchy on the columns of the matrix (e.g., Year > Month): the best month of each year, per row:

```DAX
Rank Sales = EG.RankInLevel.Compute ( [Sales Amount], SKIP, DESC, FALSE (), COLUMNS )
```

Rank within the highest level (e.g., each city among all cities of the country):

```DAX
Rank Sales = EG.RankInLevel.Compute ( [Sales Amount], SKIP, DESC, FALSE (), ROWS, HIGHESTPARENT )
```

---

## Ties

Example with five siblings, two of them tied at 400:

| Value | `SKIP` | `DENSE` | `breakTies = TRUE ()` |
| ---: | ---: | ---: | ---: |
| 600 | 1 | 1 | 1 |
| 400 | 2 | 2 | 2 |
| 400 | 2 | 2 | 3 |
| 300 | 4 | 3 | 4 |
| 200 | 5 | 4 | 5 |

- **`SKIP`:** tied rows repeat the position and the next one is skipped.
- **`DENSE`:** tied rows repeat the position and the next one is not skipped.
- **`breakTies = TRUE ()`:** among tied rows, the order follows the sort of the axis column (alphabetical, or the one set in *Sort by column*).

## Where the rank restarts

Example with Country > State > City on the rows:

| `resetLevel` | Country row | State row | City row |
| --- | --- | --- | --- |
| `LOWESTPARENT` (default) | Among all countries | Among the states of the country | Among the cities of the state |
| `HIGHESTPARENT` | Among all countries | Among the states of the country | Among the cities of the country |
| `NONE` | Among all countries | Among all states | Among all cities |

- `HIGHESTPARENT` only differs from `LOWESTPARENT` when the axis has three or more levels.
- The numeric equivalents are also accepted: `-1` (`LOWESTPARENT`), `1` (`HIGHESTPARENT`), `0` (`NONE`).
