# EG.EventsInProgress

Function to count or sum **events that are in progress on a date**: open orders, headcount, active contracts, pending tickets, occupied rooms. It works over any table with a start date and an end date, with the usual active relationship on the start date, and needs no inactive relationship or `USERELATIONSHIP`.

## What it does

An event is in progress on a date `D` when it started on or before `D` and has not ended yet:

```
start <= D  and  ( end > D  or  end is blank )
```

`EG.EventsInProgress.Compute` evaluates `<valueExpr>` over the events in progress on the dates of the current filter context. `<mode>` defines which dates are used when the context has more than one (a month, a year, the total):

| `<mode>` | Events considered | Typical use |
| --- | --- | --- |
| `"END"` (default) | In progress on the **last** visible date | Closing snapshot: open orders at month end |
| `"START"` | In progress on the **first** visible date | Opening balance |
| `"ANY"` | In progress at **any moment** of the visible period | Contracts active during the month |
| `"AVG"` | Daily average of `"END"` over the visible dates | Average headcount |

1. **Open events:** a blank `<endColumn>` means the event is still open, so it counts on every date from its start.
2. **End date:** an event that ends on `D` is no longer in progress on `D`. Set `<endInclusive>` to `TRUE` to keep it in progress on its end date.
3. **`"AVG"`** divides by the number of visible dates, including the dates with no event in progress.
4. **An invalid `<mode>`** returns `BLANK()`.

> **Note:** The function removes **only** the filters on the date table and keeps every other filter (store, product, slicers), so the events in progress are counted inside the current selection.

---

## Function

```DAX
EG.EventsInProgress.Compute (
    valueExpr,          -- value over the events in progress, e.g., COUNTROWS ( Orders )
    startColumn,        -- event start date, e.g., Orders[Order Date]
    endColumn,          -- event end date, e.g., Orders[Delivery Date]; BLANK = still open
    dateColumn,         -- date column of the date table, e.g., 'Date'[Date]
    [mode],             -- (optional) "END" (default), "START", "ANY" or "AVG"
    [additive],         -- (optional) TRUE for counts and sums in visuals by day and "AVG" (default: FALSE)
    [endInclusive]      -- (optional) TRUE keeps the event in progress on its end date (default: FALSE)
)
```

**Return:** `<valueExpr>` over the events in progress, or `BLANK()` when there are none or when `<mode>` is not one of the four values.

---

## Model requirements

### `<startColumn>` and `<endColumn>` — dates without time

Use columns of type date, without a time part. The comparison is made with the date at midnight, so an event that started at 2 PM is not in progress on that day's date when the columns hold date/times.

### `<dateColumn>` — the date column of the date table

Pass the date column of the date dimension (`'Date'[Date]`). The usual active relationship between the date table and `<startColumn>` can stay as it is.

### `<additive>` — counts and sums

| | `<additive>` = `FALSE` (default) | `<additive>` = `TRUE` |
| --- | --- | --- |
| Valid for | Any `<valueExpr>`, including distinct counts and ratios | Only values that can be added event by event: `COUNTROWS`, `SUM`, measures built on `SUMX` |
| Use in | Cards, visuals by month, quarter or year, a matrix by an attribute with few dates | Visuals by day, `"AVG"`, long date ranges |

Both return the same number for counts and sums. With a non-additive `<valueExpr>` (a distinct count), `TRUE` returns a wrong number, so keep the default.

---

## Basic usage

```DAX
Open Orders =
EG.EventsInProgress.Compute (
    COUNTROWS ( Orders ),
    Orders[Order Date],
    Orders[Delivery Date],
    'Date'[Date]
)
```

```DAX
Open Orders (any moment) =
EG.EventsInProgress.Compute (
    COUNTROWS ( Orders ),
    Orders[Order Date],
    Orders[Delivery Date],
    'Date'[Date],
    "ANY"
)
```

```DAX
Open Orders (average) =
EG.EventsInProgress.Compute (
    COUNTROWS ( Orders ),
    Orders[Order Date],
    Orders[Delivery Date],
    'Date'[Date],
    "AVG",
    TRUE ()
)
```

### Result

With four orders:

| Order | Order Date | Delivery Date |
| --- | --- | --- |
| A | 06/01 | 06/04 |
| B | 06/02 | 06/03 |
| C | 06/03 | (blank) |
| D | 06/05 | 06/06 |

A visual with `'Date'[Date]` on the rows shows:

| Date | Open Orders | Open Orders (any moment) | Open Orders (average) |
| --- | ---: | ---: | ---: |
| 06/01 | 1 | 1 | 1 |
| 06/02 | 2 | 2 | 2 |
| 06/03 | 2 | 2 | 2 |
| 06/04 | 1 | 1 | 1 |
| 06/05 | 2 | 2 | 2 |
| 06/06 | 1 | 1 | 1 |
| **Total** | **1** | **4** | **1.5** |

On a single date the three modes agree. On the total row, `"END"` returns the orders still open on 06/06 (only C), `"ANY"` returns every order open at some moment of the period, and `"AVG"` returns the average of the six daily values.

---

## Other values (optional)

Amount of the open orders, and average headcount:

```DAX
Open Orders Amount =
EG.EventsInProgress.Compute (
    [Order Amount],
    Orders[Order Date],
    Orders[Delivery Date],
    'Date'[Date],
    "END",
    TRUE ()
)
```

```DAX
Average Headcount =
EG.EventsInProgress.Compute (
    COUNTROWS ( Employee ),
    Employee[Hire Date],
    Employee[Termination Date],
    'Date'[Date],
    "AVG",
    TRUE ()
)
```

A non-additive value keeps `<additive>` as `FALSE`:

```DAX
Customers With Open Orders =
EG.EventsInProgress.Compute (
    DISTINCTCOUNT ( Orders[CustomerKey] ),
    Orders[Order Date],
    Orders[Delivery Date],
    'Date'[Date]
)
```

---

## Notes

- **Zero with `<additive>` = `TRUE`** is returned as `BLANK()`, like `COUNTROWS` over an empty table.
- **Hour grain** is out of scope.
