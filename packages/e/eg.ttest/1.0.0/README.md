# EG.TTest

Function to run a **two-sample Student's t-test** and return the **two-tailed p-value**. It answers whether the difference between the means of two groups (credit card vs. bank slip, online vs. physical store, men vs. women) is real or could be due to chance.

The two groups are two values of the same column, and each row of the table you pass is one sample.

## How to read the p-value

The p-value is the probability of observing a difference between the means **as large or larger** than the one found, **assuming there is no real difference** between the groups (the null hypothesis).

- **p below 0.05:** the difference is unlikely to be due to chance; the null hypothesis is rejected.
- **p above 0.05:** the data do not show a difference.

The p-value is **not** the probability that the null hypothesis is true, and it does not measure the size of the difference. With millions of rows, differences too small to matter to the business can give a p-value close to zero. Always show the means alongside it.

---

## Function

```DAX
EG.TTest.PValue (
    sampleTable,        -- table iterated to get the samples, e.g., Sales
    valueExpr,          -- value of each sample, evaluated row by row, e.g., Sales[Net Price]
    groupColumn,        -- column that separates the groups, e.g., Customer[Gender]
    group1,             -- value of groupColumn for the first group, e.g., "Male"
    group2,             -- value of groupColumn for the second group, e.g., "Female"
    [equalVariance]     -- (optional) FALSE = Welch test (default), TRUE = pooled test
)
```

| Parameter | Required | Default | Description |
| --- | --- | --- | --- |
| `sampleTable` | Yes | — | Table iterated to get the samples, e.g., `Sales` or `'Order Items'`. Accepts a filtered table, e.g., `FILTER ( Sales, Sales[Net Price] > 0 )`. |
| `valueExpr` | Yes | — | Value of each sample, evaluated row by row in `sampleTable`, e.g., `Sales[Net Price]` or `LN ( Sales[Net Price] )`. |
| `groupColumn` | Yes | — | Column that separates the groups, in `sampleTable` or in a related table, e.g., `Payments[payment_type]` or `Customer[Gender]`. |
| `group1` | Yes | — | Value of `groupColumn` for the first group, e.g., `"credit_card"`. |
| `group2` | Yes | — | Value of `groupColumn` for the second group, e.g., `"boleto"`. |
| `equalVariance` | No | `FALSE ()` | `FALSE ()` = Welch test, which does not assume equal variances (like type 3 of Excel's `T.TEST`). `TRUE ()` = pooled test, which assumes equal variances (type 2). |

**Return:** the two-tailed p-value, between 0 and 1, or `BLANK()` when a group has fewer than 2 samples or when both groups have no variation.

**About `equalVariance`:** prefer Welch (the default). It is correct even when the variances are equal, while the pooled test is wrong when they differ, which is common with prices.

---

## Basic usage

Credit card vs. bank slip, price per order item:

```DAX
P-Value Payment =
EG.TTest.PValue ( 'Order Items', 'Order Items'[price], Payments[payment_type], "credit_card", "boleto" )
```

With a log transformation, for long-tailed prices, excluding zero values that `LN` does not accept:

```DAX
P-Value Payment Log =
EG.TTest.PValue (
    FILTER ( 'Order Items', 'Order Items'[price] > 0 ),
    LN ( 'Order Items'[price] ),
    Payments[payment_type], "credit_card", "boleto"
)
```

Pooled test (equal variances):

```DAX
P-Value Payment Pooled =
EG.TTest.PValue ( 'Order Items', 'Order Items'[price], Payments[payment_type], "credit_card", "boleto", TRUE () )
```

Online store vs. United States stores, in Contoso:

```DAX
P-Value Online vs US =
EG.TTest.PValue ( Sales, Sales[Net Price], Store[Country], "Online", "United States" )
```

Significance flag for conditional formatting:

```DAX
Is Significant =
VAR _P = [P-Value Payment Log]
RETURN
    IF ( NOT ISBLANK ( _P ), _P < 0.05 )
```

### Building the visual

1. A **table** with the category on the rows, e.g., `Products[product_category_name]`.
2. **Values:** the mean of each group (e.g., `CALCULATE ( AVERAGE ( 'Order Items'[price] ), Payments[payment_type] = "credit_card" )`), the number of samples of each group (`COUNTROWS` instead of `AVERAGE`) and `[P-Value Payment Log]`.
3. **Sort by the p-value** ascending: the categories at the top have the most reliable difference between the groups.
4. **Conditional formatting** by `[Is Significant]`.

The p-value does not change when the report filters the group column itself: the function always compares the two groups passed. Categories with few samples in one group tend to have a high p-value, or are blank with fewer than 2 samples.

---

## How it works

1. **Statistics of both groups in one table:** count, mean and sample variance of each group (`COUNTX`, `AVERAGEX`, `VARX.S`), so the engine solves both groups together.
2. **Standard error and degrees of freedom:**
   - Welch: sum of each group's variance divided by its size, with Welch–Satterthwaite (fractional) degrees of freedom;
   - pooled: combined variance, with `n1 + n2 − 2` degrees of freedom.
3. **t statistic** = difference of the means ÷ standard error.
4. **Two-tailed p-value** from the t distribution, computed as `BETA.DIST ( df / ( df + t² ), df / 2, 0.5, TRUE )`.

### Precision

Two details that change the result and that the function already handles:

- **Currency columns.** Arithmetic on Currency is rounded to four decimals. In Contoso, the rounded mean of `Sales[Net Price]` changed the p-value by up to 0.00045, because the difference between the groups is small. The function converts the value to `DOUBLE` before computing.
- **Fractional degrees of freedom.** DAX's `T.DIST.2T` rounds the degrees of freedom to the nearest integer (5.4 becomes 5; 5.9 becomes 6). With large samples it makes no difference; with small samples it does:

  | Samples | Degrees of freedom (Welch) | Exact p | `T.DIST.2T` |
  | --- | ---: | ---: | ---: |
  | 6 and 9 | 12.35 | 0.135082 | 0.135802 |
  | 4 and 12 | 13.90 | 0.250201 | 0.250053 |
  | 3 and 25 | 5.85 | 0.098385 | 0.097160 |

  The `BETA.DIST` identity accepts fractional degrees of freedom and returns the exact value.

The p-values were checked against an independent Python calculation over 10,144,064 Contoso sales rows: 2 tests, Welch and pooled, with and without log, at total, category, subcategory, brand and product level (20,552 p-values), with a maximum difference of 1.4 × 10⁻⁹.

---

## Limitations

- **Each row of `sampleTable` is one sample.** If the same row can fall into both groups (e.g., an order paid with both credit card and bank slip), it counts in both. Filter `sampleTable` to keep only samples of a single group, e.g., orders with one payment type.
- **`LN` requires values greater than zero.** Filter the table or use `LN ( value + 1 )`.
- **The test assumes approximately normal means.** With large samples this holds even for skewed data; with small, long-tailed samples, the log transformation helps.
- **Compares exactly two values of one column.** For "one group vs. the rest", create a column with the two labels.
