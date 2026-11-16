# EG.CrossSell

Function for **cross-sell (market basket) analysis**: for the product the user selects, it shows which other products are bought together with it, and how strongly.

The user selects the product in a slicer on the `Product` table, and the rows of the visual come from a **disconnected copy** of that table (`auxProduct`). For each row, the function counts how many baskets (orders or customers) have the selected product **and** the row product.

All examples use the **Contoso** model.

## Metrics

| `metric` | What it returns | Contoso example |
| --- | --- | --- |
| `"COUNT"` (default) | Baskets that have both products. | How many orders have the *Adventure Works Desktop PC1.80 ED180 Brown* and the row product. |
| `"SHARE"` | Of the baskets with the selected product, the fraction that also has the row product. It is the **confidence** of the association rule. | Of the 2,388 orders in 2019 with that desktop, the fraction that also included the row product. |
| `"LIFT"` | `SHARE` ÷ the fraction of all baskets that have the row product. Measures how much the joint purchase beats chance. | Lift 2.0: those who buy the desktop take the row product twice as often as the average of the 651 thousand orders in 2019. |

**Reading the lift:**

- **Lift above 1:** the products are bought together more than expected. Good candidates for bundles, joint display or recommendations.
- **Lift close to 1:** there is no association; a high `SHARE` comes only from the row product being popular.
- **Lift below 1:** the products tend not to be bought together.

Use `SHARE` to know **what** goes with the product and `LIFT` to know **what is specific** to it. A best-selling product shows up at the top of the `SHARE` of almost every product.

---

## Function

```DAX
EG.CrossSell.Compute (
    basketColumn,           -- fact column that identifies the basket, e.g., Sales[Order Key]
    factProductColumn,      -- product column in the fact table, e.g., Sales[ProductKey]
    selectedProductColumn,  -- key of the related product table, where the user selects, e.g., Product[ProductKey]
    rowProductColumn,       -- key of the disconnected copy, on the rows of the visual, e.g., auxProduct[ProductKey]
    [metric]                -- (optional) "COUNT" (default), "SHARE" or "LIFT"
)
```

| Parameter | Required | Default | Description |
| --- | --- | --- | --- |
| `basketColumn` | Yes | — | Column of `Sales` that defines the basket: `Sales[Order Key]` (order) or `Sales[CustomerKey]` ("customers who bought A also bought B"). |
| `factProductColumn` | Yes | — | Product column in `Sales`: `Sales[ProductKey]`. |
| `selectedProductColumn` | Yes | — | Key of the `Product` table, related to `Sales`, where the user selects: `Product[ProductKey]`. The filters on that table define the selected products. |
| `rowProductColumn` | Yes | — | Key of the disconnected copy, used on the rows of the visual: `auxProduct[ProductKey]`. |
| `metric` | No | `"COUNT"` | `"COUNT"`, `"SHARE"` or `"LIFT"`. Case-insensitive; an invalid value raises an error listing the options. |

**Return:** the metric for the product (or group of products) on the row. Blank on the rows of the selected products themselves and on products never bought together with them.

The other report filters (year, store, customer country) apply to both sides of the analysis.

### Model setup

- A column that identifies the **basket** in the `Sales` table. Contoso has no order number, so the order is approximated by customer + day:

  ```DAX
  Order Key = Sales[CustomerKey] * 100000 + INT ( Sales[Order Date] )
  ```

  For cross-sell by customer, use `Sales[CustomerKey]` directly, with no new column.

- A **disconnected copy** of `Product` for the rows of the visual, with no relationship to `Sales`:

  ```DAX
  auxProduct =
  SELECTCOLUMNS (
      Product,
      "ProductKey", Product[ProductKey],
      "Product Name", Product[Product Name],
      "Subcategory", Product[Subcategory],
      "Category", Product[Category],
      "Brand", Product[Brand]
  )
  ```

---

## Basic usage

```DAX
Cross Sell Orders =
EG.CrossSell.Compute ( Sales[Order Key], Sales[ProductKey], Product[ProductKey], auxProduct[ProductKey] )
```

```DAX
Cross Sell % =
EG.CrossSell.Compute ( Sales[Order Key], Sales[ProductKey], Product[ProductKey], auxProduct[ProductKey], "SHARE" )
```

```DAX
Cross Sell Lift =
EG.CrossSell.Compute ( Sales[Order Key], Sales[ProductKey], Product[ProductKey], auxProduct[ProductKey], "LIFT" )
```

The ranking is still a regular measure. It must call a **measure**, not the function directly, so that there is a context transition on each row:

Cross-sell by customer instead of by order:

```DAX
Cross Sell % Customers =
EG.CrossSell.Compute ( Sales[CustomerKey], Sales[ProductKey], Product[ProductKey], auxProduct[ProductKey], "SHARE" )
```

### Building the visual

1. A **slicer** with `Product[Product Name]` (single selection), where the user picks the product. To test, *Adventure Works Desktop PC1.80 ED180 Brown*, the product with the most orders.
2. A **slicer** with `'Date'[Year]`, optional.
3. A **table** with `auxProduct[Product Name]` on the rows.
4. **Values:** `[Cross Sell Orders]`, `[Cross Sell %]`, `[Cross Sell Lift]` and `[Cross Sell Rank]`.
5. **Sort** by the percentage or by the lift, descending.

The rows can be another level of the copy, such as `auxProduct[Category]` or `auxProduct[Brand]`. Then the metric counts the baskets that have the selected product and any product of the category.

What to check:

- **Row of the selected product:** it is blank.
- **Total of the count column:** it is the number of baskets with the selected product, not the sum of the rows, because one basket can have several other products.
- **With several products selected** (the whole *Desktops* subcategory, for example): the metric counts the baskets with **any** of them, and the rows of those products are blank.

---

## How it works

1. **Baskets of the selected product:** the list of baskets in the current context, already filtered by the selection on `Product`.
2. **Co-occurrence table:** without the filters on `Product` and restricted to those baskets, one row per product with how many of them contain it, leaving out the selected products themselves. This table is the same for every row of the visual, so the engine builds it once and each row only looks up its product.
3. **Rows with several products** (category, total): direct calculation, which counts each basket once.
4. **`SHARE`:** divides by the number of baskets of the selected product.
5. **`LIFT`:** divides `SHARE` by the fraction of all baskets that have the row product (without the selection). Those counts are added to the co-occurrence table, so they are computed once and only for the products bought together with the selection.
