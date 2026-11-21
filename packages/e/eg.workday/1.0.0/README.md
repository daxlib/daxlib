# EG.Workday

Function that returns the **date a number of business days after (or before) another date**, like `WORKDAY` and `WORKDAY.INTL` in Excel: due dates, delivery promises, "5 business days to answer". DAX has `NETWORKDAYS`, which counts the business days between two dates, but no function that adds business days to a date.

## What it does

`EG.Workday.Compute` moves a date forward or backward by a number of business days, skipping weekends and holidays. It follows the rules of `WORKDAY` in Excel:

1. **The start date is not counted.** From a Friday, `1` returns the next Monday.
2. **Starting on a non-business day:** from a Saturday, `1` returns the next Monday and `-1` returns the previous Friday.
3. **`<days>`:** a positive number goes forward, a negative number goes backward and `0` returns `<startDate>`.
4. **Time part:** a time in `<startDate>` is ignored, so the result is always a date.

Weekend days and holidays come in as parameters, so nothing in the model is assumed.

> **Note:** The function is scalar: use it in a calculated column or inside an iterator (`MAXX`, `SUMX`, `FILTER`). Dates between 1900 and 2199 are supported, for the start date and for the result.

---

## Function

```DAX
EG.Workday.Compute (
    startDate,          -- start date, e.g., Orders[Order Date]; it is not counted
    days,               -- business days to add; negative goes backwards; 0 returns startDate
    [weekend],          -- (optional) weekend days, same codes as NETWORKDAYS (default: 1 = Saturday and Sunday)
    [holidays]          -- (optional) single-column table of dates that are not business days (default: none)
)
```

**Return:** a date between 1900 and 2199, or `BLANK()` when `<startDate>` is blank or the result falls outside that range.

---

## Model requirements

### `<weekend>` — the weekend days

Same codes as `NETWORKDAYS`: `1` Saturday and Sunday, `2` Sunday and Monday, ..., `7` Friday and Saturday, `11` Sunday only, ..., `17` Saturday only.

### `<holidays>` — a table of dates (optional)

A single-column table of type date with the days that are not business days, such as a `Holidays` table. Holidays that fall on a weekend are ignored, as in `NETWORKDAYS`.

---

## Basic usage

Calculated columns in a `Tickets` table:

```DAX
Due Date =
EG.Workday.Compute ( Tickets[Opened], 5, 1, Holidays )
```

`<days>` can also be a column, such as the SLA days of each ticket:

```DAX
Due Date (by priority) =
EG.Workday.Compute ( Tickets[Opened], Tickets[SLA Days], 1, Holidays )
```

```DAX
Is Late =
Tickets[Closed] > EG.Workday.Compute ( Tickets[Opened], 5, 1, Holidays ) + 1
```

### Result

Weekend on Saturday and Sunday, May 1 as a holiday:

| Start | Days | Result |
| --- | ---: | --- |
| Fri Jun 14, 2019 | 1 | Mon Jun 17, 2019 |
| Sat Jun 15, 2019 | -1 | Fri Jun 14, 2019 |
| Tue Apr 30, 2019 | 1 | Thu May 2, 2019 (May 1 is a holiday) |
| Fri Jun 14, 2019 16:30 | 1 | Mon Jun 17, 2019 (the time is ignored) |

---

## Going backward with another weekend (optional)

Ten business days before the end of a contract, with a week from Sunday to Thursday (weekend on Friday and Saturday):

```DAX
Review Date =
EG.Workday.Compute ( Contracts[End Date], -10, 7, Holidays )
```
