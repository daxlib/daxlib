# EG.BusinessHours

Function to compute the **business hours between two date/times**, considering opening hours, weekends and holidays: SLA of tickets, lead time of orders, time to first response. DAX has `NETWORKDAYS`, which counts whole business days, but nothing that counts business hours.

## What it does

`EG.BusinessHours.Compute` returns the decimal number of business hours between a start and an end, counting only the opening hours of the business days:

1. **Business days:** every day that is not a weekend day (`<weekend>`) or a holiday (`<holidays>`) counts `<closeTime> − <openTime>` hours.
2. **First and last day:** only the part inside the opening hours counts. A start before the opening time counts from the opening; an end after the closing time counts up to the closing.
3. **Outside business days:** a start or an end on a weekend or on a holiday adds no hours for that day.
4. **Invalid intervals:** a blank date or an end before the start returns `BLANK()`.

Opening hours, weekend days and holidays come in as parameters, so nothing in the model is assumed.

> **Note:** The function is scalar: use it in a calculated column or inside an iterator (`AVERAGEX`, `SUMX`, `MAXX`).

---

## Function

```DAX
EG.BusinessHours.Compute (
    startDateTime,      -- start date and time, e.g., Tickets[Opened]
    endDateTime,        -- end date and time, e.g., Tickets[Closed]
    openTime,           -- opening time of a business day, e.g., TIME ( 8, 0, 0 )
    closeTime,          -- closing time of a business day, e.g., TIME ( 18, 0, 0 )
    [weekend],          -- (optional) weekend days, same codes as NETWORKDAYS (default: 1 = Saturday and Sunday)
    [holidays]          -- (optional) single-column table of dates that are not business days (default: none)
)
```

**Return:** decimal hours (`4.5` is four and a half hours), or `BLANK()` when a date is blank or the end is before the start.

---

## Model requirements

### `<openTime>` and `<closeTime>` — the opening hours

Only the time part is used. `<closeTime>` must be later than `<openTime>`. `TIME ( 0, 0, 0 )` as closing time means midnight at the end of the day, so `TIME ( 0, 0, 0 )` to `TIME ( 0, 0, 0 )` is a 24-hour business day.

### `<weekend>` — the weekend days

Same codes as `NETWORKDAYS`: `1` Saturday and Sunday, `2` Sunday and Monday, ..., `7` Friday and Saturday, `11` Sunday only, ..., `17` Saturday only.

### `<holidays>` — a table of dates (optional)

A single-column table of type date with the days that are not business days, such as a `Holidays` table. Holidays that fall on a weekend are ignored, as in `NETWORKDAYS`.

---

## Basic usage

Calculated column in a `Tickets` table:

```DAX
Business Hours =
EG.BusinessHours.Compute (
    Tickets[Opened],
    Tickets[Closed],
    TIME ( 8, 0, 0 ),
    TIME ( 18, 0, 0 ),
    1,
    Holidays
)
```

Measures over the calculated column, or directly with an iterator:

```DAX
Avg Business Hours =
AVERAGE ( Tickets[Business Hours] )
```

```DAX
Avg Business Hours (iterator) =
AVERAGEX (
    Tickets,
    EG.BusinessHours.Compute (
        Tickets[Opened],
        Tickets[Closed],
        TIME ( 8, 0, 0 ),
        TIME ( 18, 0, 0 ),
        1,
        Holidays
    )
)
```

### Result

Opening hours 8:00 to 18:00, weekend on Saturday and Sunday, May 1 as a holiday:

| Start | End | Business hours | Why |
| --- | --- | ---: | --- |
| Fri Jun 14, 2019 16:00 | Mon Jun 17, 2019 10:00 | 4 | 2 hours on Friday and 2 on Monday |
| Sat Jun 15, 2019 10:00 | Sun Jun 16, 2019 12:00 | 0 | Weekend only |
| Wed Jun 12, 2019 07:00 | Wed Jun 12, 2019 20:00 | 10 | Cut to the opening hours |
| Tue Apr 30, 2019 17:00 | Thu May 2, 2019 09:00 | 2 | Wednesday is a holiday |
| Fri Jun 14, 2019 16:00 | Thu Jun 13, 2019 10:00 | | End before start |

---

## Other calendars (optional)

A week from Sunday to Thursday (weekend on Friday and Saturday), 9:00 to 17:30:

```DAX
Business Hours (Sun-Thu) =
EG.BusinessHours.Compute (
    Tickets[Opened],
    Tickets[Closed],
    TIME ( 9, 0, 0 ),
    TIME ( 17, 30, 0 ),
    7
)
```

A 24-hour operation, closed on Sundays and holidays:

```DAX
Operating Hours =
EG.BusinessHours.Compute (
    Tickets[Opened],
    Tickets[Closed],
    TIME ( 0, 0, 0 ),
    TIME ( 0, 0, 0 ),
    11,
    Holidays
)
```

---

## Notes

- **Overnight shifts** (closing time earlier than opening time), different hours per day of the week and time zones are out of scope.
- **Lunch breaks:** add two calls, one for the morning hours and one for the afternoon hours.
