# EG.ConsecutivePeriods

Function to count **consecutive periods** in which a condition is TRUE, ending at the period in context. A single function replaces measures such as "days in a row with sales", "days in a row without sales", "consecutive days worked" and "consecutive absences" per employee.

## What it does

`EG.ConsecutivePeriods.Count` returns the length of the streak of periods in which `<conditionExpr>` is TRUE, ending at the reference period:

1. **Reference period:** the period in context. With a single period (a row of the visual) it is `MAX ( <periodColumn> )`. With several periods (total, month, year) it is the last of them that meets the condition, so the total row is not blank.
2. **Selection:** the history considered is the periods selected in the report (`ALLSELECTED` on the period table). With a year slicer set to 2018, the count restarts at 1 on 01/01/2018.
3. **Breaks:** the selected periods in which the condition is FALSE. This table does not depend on the row of the visual, so the engine computes it once per query.
4. **Result:** `reference period − last break`. When there is no break before the reference period, the first selected period minus 1 is used. When the reference period is itself a break, the function returns `BLANK()`.

> **Note:** The function removes **only** the filters on the period table and keeps every other filter (employee, store, color, slicers). If the visual has row attributes that do not define the series (e.g., shift start/end, unit), `<conditionExpr>` must clear them with `REMOVEFILTERS`.

---

## Function

```DAX
EG.ConsecutivePeriods.Count (
    periodColumn,       -- gapless period column, e.g., 'Date'[Date] or 'Date'[Year Month Index]
    conditionExpr       -- boolean condition evaluated per period, e.g., [Sales Amount] > 0
)
```

**Return:** the number of consecutive periods in which `<conditionExpr>` is TRUE, ending at the reference period, or `BLANK()` when the condition is not met.

---

## Model requirements

### `<periodColumn>` — a gapless sequence

The result is computed as a **difference** between periods, so `<periodColumn>` must be a sequence without gaps: a date, or a sequential month/week index (see the last section). A code such as `202412 → 202501` does not work, because the difference between consecutive months is not 1.

---

## Basic usage

### Days in a row with / without sales

A visual with `'Date'[Date]` on the rows:

```DAX
Days With Sales Streak =
EG.ConsecutivePeriods.Count ( 'Date'[Date], [Sales Amount] > 0 )
```

```DAX
Days Without Sales Streak =
EG.ConsecutivePeriods.Count ( 'Date'[Date], [Sales Amount] = 0 )
```

### Result

With sales on 01/01 to 01/03, no sales on 01/04 and sales again on 01/05 and 01/06:

| Date | Sales Amount | Days With Sales Streak | Days Without Sales Streak |
| --- | ---: | ---: | ---: |
| 01/01 | 120.00 | 1 | |
| 01/02 | 80.00 | 2 | |
| 01/03 | 95.00 | 3 | |
| 01/04 | 0.00 | | 1 |
| 01/05 | 60.00 | 1 | |
| 01/06 | 140.00 | 2 | |
| **Total** | **495.00** | **2** | **1** |

On the total row the reference period is the last date that meets each condition: 01/06 for sales (streak of 2) and 01/04 for no sales (streak of 1).

---

## Usage per entity: consecutive days worked

The function keeps the filters on other tables, so the same pattern works per employee. In the example below, `'Time Entries'` holds the employees' clock-in/clock-out records from the time tracking system (one or more rows per employee and day worked), and the visual has the employee and `'Date'[Date]` on the rows:

```DAX
Consecutive Days Worked =
EG.ConsecutivePeriods.Count (
    'Date'[Date],
    NOT ISEMPTY ( 'Time Entries' )
)
```

The employee filter of each row is kept, so the streak is counted per employee.

---

## Months in a row with sales (optional)

Any gapless period column works. To count months instead of days, add a sequential month index to the date table:

```DAX
Year Month Index = 'Date'[Year] * 12 + 'Date'[Month Number]    -- calculated column in 'Date'
```

```DAX
Months With Sales Streak =
EG.ConsecutivePeriods.Count ( 'Date'[Year Month Index], [Sales Amount] > 0 )
```
