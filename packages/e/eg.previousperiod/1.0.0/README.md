# EG.PreviousPeriod

Function to compare any date selection with the **window immediately before it, with the same length**: the 14 days before a 14-day selection, the previous month for a whole month, the previous quarter for a whole quarter. No fixed time unit is needed, so the same measure works with a date slicer, a month matrix or a daily chart.

It works with both kinds of time intelligence:

- **Classic:** pass the date column of a date table, such as `'Date'[Date]`.
- **Calendar-based:** pass a calendar defined in the model, such as `'Gregorian'`, a fiscal calendar or a 4-4-5 calendar. "Month" then means a month of that calendar, so a 5-week fiscal period is compared with the fiscal period before it.

## What it does

`DATEADD` and `SAMEPERIODLASTYEAR` need a fixed unit (day, month, year). When the user picks an arbitrary range, such as March 10 to March 23, the usual question is "how does it compare with the 14 days before?". `EG.PreviousPeriod.Compute` evaluates `<valueExpr>` over that previous window:

| Visible dates | Previous window used |
| --- | --- |
| Mar 10 to Mar 23 (14 days) | Feb 24 to Mar 9 |
| A single day | The day before |
| March, whole month | February, whole month |
| Q3, whole quarter | Q2, whole quarter |
| A whole year | The previous year |
| A 5-week fiscal period, with a 4-4-5 calendar | The fiscal period before it (4 weeks) |
| The same 5-week period, with a date column | The 35 days before it |

1. **Whole months:** when the visible dates are exactly whole months (months of the calendar, when a calendar is passed), the window shifts by that number of months. March (31 days) returns February, not January 29 to February 28. Set `<alignToMonths>` to `FALSE` to always shift by the number of days.
2. **Other selections:** the visible dates shift back by their number of days.
3. **`<periods>`:** `2` returns the window before the previous one, `-1` returns the next window and `0` returns the visible window.

> **Note:** With a date column, the filters on the date table are replaced by the previous window. With a calendar, the filters on the columns of the calendar are replaced and the filters on columns that are not in the calendar are kept. Filters on other tables (store, product, slicers) are always kept. Other filters for the previous window go inside `<valueExpr>` with `CALCULATE`.

---

## Function

```DAX
EG.PreviousPeriod.Compute (
    valueExpr,          -- value to compute, e.g., [Sales Amount]
    dates,              -- date column, e.g., 'Date'[Date], or a calendar, e.g., 'Gregorian'
    [periods],          -- (optional) how many windows to go back; negative goes forward (default: 1)
    [alignToMonths]     -- (optional) whole months shift by months (default: TRUE)
)
```

**Return:** `<valueExpr>` computed over the previous window, or `BLANK()` when no date is visible.

---

## Model requirements

### `<dates>` as a date column

Pass the date column of the date dimension (`'Date'[Date]`), not a date column of the fact table. The date table must be marked as a date table, or related to the facts by a column of type date, as for any time-intelligence function.

### `<dates>` as a calendar

The calendar needs the **Month** and **Date** categories, because the window shifts by months and by days.

---

## Basic usage

With the date column:

```DAX
Sales Previous Period =
EG.PreviousPeriod.Compute ( [Sales Amount], 'Date'[Date] )
```

With a calendar:

```DAX
Sales Previous Period =
EG.PreviousPeriod.Compute ( [Sales Amount], 'Gregorian' )
```

```DAX
Sales Previous Fiscal Period =
EG.PreviousPeriod.Compute ( [Sales Amount], 'Fiscal 4-4-5' )
```

Growth, two windows back and the next window:

```DAX
Sales vs Previous Period % =
VAR _Previous = EG.PreviousPeriod.Compute ( [Sales Amount], 'Gregorian' )
RETURN
    DIVIDE ( [Sales Amount] - _Previous, _Previous )
```

```DAX
Sales 2 Periods Back =
EG.PreviousPeriod.Compute ( [Sales Amount], 'Gregorian', 2 )
```

```DAX
Sales Next Period =
EG.PreviousPeriod.Compute ( [Sales Amount], 'Gregorian', -1 )
```

Other filters go inside `<valueExpr>`, which is evaluated in the previous window:

```DAX
Sales Previous Period Italy =
EG.PreviousPeriod.Compute (
    CALCULATE ( [Sales Amount], Store[Country] = "Italy" ),
    'Gregorian'
)
```

### Result

With a date slicer on June 10 to June 23, 2019 (14 days), with `'Date'[Date]` or with `'Gregorian'`:

| Measure | Window | Value |
| --- | --- | ---: |
| `[Sales Amount]` | Jun 10 to Jun 23 | 51,874,521.87 |
| `Sales Previous Period` | May 27 to Jun 9 | 51,104,107.52 |
| `Sales 2 Periods Back` | May 13 to May 26 | 51,268,397.87 |
| `Sales Next Period` | Jun 24 to Jul 7 | 50,397,369.60 |
| `Sales Previous Period Italy` | May 27 to Jun 9 | 386,067.69 |

Each measure keeps the 14-day length and moves the window back (or forward) by 14 days for each period.

With a 4-4-5 calendar and the fiscal period of February 10 to March 16, 2019 (5 weeks) selected:

| `<dates>` | Window | Value |
| --- | --- | ---: |
| The 4-4-5 calendar | Previous fiscal period, Jan 13 to Feb 9 (4 weeks) | 113,572,614.98 |
| `'Date'[Date]` or `'Gregorian'` | Previous 35 days, Jan 6 to Feb 9 | 147,411,092.26 |

The calendar compares fiscal period with fiscal period; the date column keeps the 35-day length.

---

## Shifting by days, even for whole months (optional)

With `<alignToMonths>` set to `FALSE`, a whole month is compared with the same number of days immediately before it:

```DAX
Sales Previous Same Days =
EG.PreviousPeriod.Compute ( [Sales Amount], 'Gregorian', 1, FALSE () )
```

For March (31 days), `Sales Previous Period` returns February, while `Sales Previous Same Days` returns January 29 to February 28.

---

## Notes

- **Not contiguous selections** (only the Mondays of a month) move every selected date back by the number of selected dates. With the four Mondays of June, each date moves back 4 days.
- **Slicers on attributes of the date table** (weekday, working day) change the number of visible dates, and so the length of the window. Put those filters inside `<valueExpr>` instead: `CALCULATE ( [Sales Amount], 'Date'[Working Day] = TRUE () )`.
- **The previous window is not truncated.** If the current month is still in progress and the whole month is visible, a partial month is compared with a full one. Limit the visible dates (e.g., a slicer up to the last date with data) to compare like with like.
