# EG.MergedIntervals

Function to compute the **total duration of overlapping time intervals**, counting the overlapped time **only once**. A single function replaces measures such as "machine downtime hours" with simultaneous failure events, "hours worked" with overlapping tasks, or "time with an open incident" with concurrent tickets.

## What it does

Summing `End − Start` overcounts whenever intervals overlap: two failures of the same machine from 08:00 to 10:00 and from 09:00 to 11:00 add up to 4 hours, but the machine was stopped for 3.

`EG.MergedIntervals.Duration` merges the intervals of each group into continuous blocks and returns the sum of the length of the blocks:

1. **Intervals:** one row per group and start, taken from the filter context. When several intervals share the same start, only the longest one is kept. Intervals with a blank end or an end not after the start are ignored.
2. **Groups:** intervals are merged only within the same value of `<groupColumn>` (e.g., per machine). Each group gets its own range of keys, so all groups are processed at once.
3. **Blocks:** a start opens a block when every interval that started before it has already ended; an end closes a block when no interval is still open after it. Overlapping **and touching** intervals (one ends exactly when the next starts) fall in the same block.
4. **Result:** `Σ block ends − Σ block starts`, computed in whole seconds and converted to the unit set by `<unitsPerDay>`.

There are no calculated columns and no row-by-row iteration to find the overlaps, so the function stays fast on large interval tables.

> **Note:** an interval is included whole when its row is visible in the filter context; it is not clipped to the dates selected in the report.

---

## Function

```DAX
EG.MergedIntervals.Duration (
    startColumn,        -- start of the interval (date/time), e.g., Downtime[Start]
    endColumn,          -- end of the interval (date/time), e.g., Downtime[End]
    groupColumn,        -- intervals are merged only within the same value, e.g., Downtime[Machine]
    [unitsPerDay]       -- (optional) unit of the result: 24 hours (default), 1440 minutes, 86400 seconds, 1 days
)
```

| Parameter | Required | Default | Values | Description |
| --- | --- | --- | --- | --- |
| `startColumn` | Yes | — | A date/time column, e.g., `Downtime[Start]` | Start of the interval. |
| `endColumn` | Yes | — | A date/time column, e.g., `Downtime[End]` | End of the interval. |
| `groupColumn` | Yes | — | A column, e.g., `Downtime[Machine]` | Intervals of different groups never merge; the result is the sum over all groups. |
| `unitsPerDay` | No | `24` | `1`, `24`, `1440`, `86400` | Unit of the result: days, hours, minutes or seconds. |

**Return:** the total duration of the union of the intervals, summed over all groups, or `BLANK()` when there are no valid intervals. The precision is one second.

---

## Basic usage

A `Downtime` table with one row per failure event, and a visual with `Downtime[Machine]` on the rows:

```DAX
Downtime Hours =
EG.MergedIntervals.Duration ( Downtime[Start], Downtime[End], Downtime[Machine] )
```

Same result in minutes:

```DAX
Downtime Minutes =
EG.MergedIntervals.Duration ( Downtime[Start], Downtime[End], Downtime[Machine], 1440 )
```

### Data

| Machine | Start | End |
| --- | --- | --- |
| A | 08:00 | 10:00 |
| A | 09:00 | 11:00 |
| A | 11:00 | 12:00 |
| A | 14:00 | 15:00 |
| B | 08:00 | 09:30 |
| B | 08:00 | 09:00 |
| B | 10:00 | 10:30 |

### Result

| Machine | Sum of `End − Start` (hours) | Downtime Hours |
| --- | ---: | ---: |
| A | 6.0 | 5.0 |
| B | 3.0 | 2.0 |
| **Total** | **9.0** | **7.0** |

- **Machine A:** 08:00–10:00 and 09:00–11:00 overlap, and 11:00–12:00 touches them, forming the block 08:00–12:00 (4 h). 14:00–15:00 is a separate block (1 h).
- **Machine B:** 08:00–09:00 starts with 08:00–09:30 and is inside it, so the block is 08:00–09:30 (1.5 h). 10:00–10:30 adds 0.5 h.
- **Total:** the blocks of each machine are summed; overlaps between different machines are not merged.

---

## Choosing the group

`<groupColumn>` defines **what** cannot be counted twice:

| Question | `<groupColumn>` |
| --- | --- |
| How long was each machine stopped? | `Downtime[Machine]` |
| How many hours did each employee work, with overlapping tasks? | `Tasks[Employee]` |
| How long did each customer have at least one open ticket? | `Tickets[Customer]` |

The other filters of the visual (date, line, plant) are kept, so the same measure works on any breakdown. To merge **all** intervals into one timeline, pass a column with a single value in the filter context.
