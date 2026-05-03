# EG.RFMClassification

Functions to implement the RFM (Recency, Frequency, Monetary) customer segmentation pattern in DAX, using a dynamic scoring approach based on percentile quintiles.

The classification concept follows a similar structure to the [DaxPatterns.AbcClassification](https://daxlib.org/package/DaxPatterns.AbcClassification/) package: a disconnected score table defines the class boundaries, and a function evaluates any measure restricted to the customers in each class.

## What it does

`EG.RFMClassification.ComputeInRFMClass` evaluates an expression restricted to the customers (or items) that belong to the RFM class active in the current filter context from a disconnected `Score_RFM` table.

`EG.RFMClassification.ItemsInClass` can be called directly to inspect the full scored customer table.

> **Warning:** The scoring uses `PERCENTILEX.EXC` to compute quintile boundaries. This function requires **at least 4 items** in the customer base to produce valid percentile values. If the fact table contains fewer than 4 distinct customers (or items) after applying the current filter context, the function will return an error.

---

## Function

### `EG.RFMClassification.ComputeInRFMClass`

```DAX
EG.RFMClassification.ComputeInRFMClass (
    <valueExpr>,                -- e.g., [Qty Customers]
    <todayDate>,                -- e.g., TODAY()
    <MonetaryExpr>,             -- e.g., [Net Revenue]
    <transactionsDateColumn>,   -- e.g., Sales[Date]
    <ItemKeyColumn>,            -- e.g., Customer[Customer Id]
    <RFMScoreColumn>            -- e.g., Score_RFM[RFM Score]
)
```

**Return:** The result of `<valueExpr>` calculated only for customers whose RFM score matches the current filter context on `<RFMScoreColumn>`.

---

## Model requirements

1. **Transactions table:** Must contain at least a date column and a customer (item) key column.

   - **`<valueExpr>`** is the measure you want to analyze per RFM class — for example, the count of customers or total orders. This is the measure that will be filtered and returned by `ComputeInRFMClass`.
   - **`<MonetaryExpr>`** is the monetary measure used **exclusively for scoring** — for example, net revenue or sales amount per customer. It is used internally to rank customers by their spending and assign the Monetary score. It is not the value returned by the function.

2. **Score table (disconnected):** A lookup table mapping each numeric RFM score to a class description. No relationships to other tables. The RFM score is a two-digit number where the tens digit is the combined Frequency-Monetary score (1–5) and the units digit is the Recency score (1–5).

   ```DAX
   Score_RFM =
   DATATABLE (
       "RFM Description", STRING,
       "RFM Score",       INTEGER,
       {
           { "1 - Champion",            55 },
           { "2 - Loyal Customers",     54 },
           { "2 - Loyal Customers",     45 },
           { "2 - Loyal Customers",     44 },
           { "2 - Loyal Customers",     35 },
           { "2 - Loyal Customers",     34 },
           { "3 - Potential Loyalist",  53 },
           { "3 - Potential Loyalist",  52 },
           { "3 - Potential Loyalist",  43 },
           { "3 - Potential Loyalist",  42 },
           { "4 - New Customers",       51 },
           { "5 - Promising Customers", 41 },
           { "6 - Needs Attention",     33 },
           { "7 - Almost Sleeping",     32 },
           { "7 - Almost Sleeping",     31 },
           { "8 - At Risk",             25 },
           { "8 - At Risk",             24 },
           { "8 - At Risk",             23 },
           { "8 - At Risk",             14 },
           { "8 - At Risk",             13 },
           { "9 - Hibernating",         22 },
           { "10 - Can't Lose Them",    15 },
           { "11 - Lost",               21 },
           { "11 - Lost",               12 },
           { "11 - Lost",               11 }
       }
   )
   ```

---

## How scoring works

Each customer receives three scores computed via `PERCENTILEX.EXC` quintiles (0.2 / 0.4 / 0.6 / 0.8) over the full customer base:

| Score | Dimension | Rule |
|---|---|---|
| **R (Recency)** | Days since last purchase | Higher days → higher score (1 = most recent, 5 = least recent) |
| **F (Frequency)** | Number of transactions | Higher count → higher score |
| **M (Monetary)** | Sum of the monetary measure | Higher value → higher score |

The F and M scores are averaged and rounded to form a single **FM score** (1–5). The final two-digit **RFM score** is `FM × 10 + R`. This score is then matched against the `Score_RFM` table to resolve the class label.

---

## Basic usage

```DAX
Qty Customers RFM =
EG.RFMClassification.ComputeInRFMClass (
    [Qty Customers],
    TODAY (),
    [Net Revenue],
    Sales[Date],
    Customer[Customer Id],
    Score_RFM[RFM Score]
)
```

Place this measure in a matrix or bar chart with `Score_RFM[RFM Description]` on the rows/axis. When filtered by a class, the function automatically restricts to the customers in that segment.

---

## Related reading

* [DaxPatterns.AbcClassification](https://daxlib.org/package/DaxPatterns.AbcClassification/) — similar pattern using a disconnected class table.
