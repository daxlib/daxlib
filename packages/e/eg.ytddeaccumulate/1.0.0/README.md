# EG.YTDDeAccumulate

Function to convert YTD (Year-to-Date) cumulative values into individual monthly values by computing the incremental difference between consecutive months.

## What it does

`EG.YTDDeAccumulate.Compute` takes a measure that accumulates values throughout the year (YTD) and returns the value for each month individually. For January, the full YTD value is returned since there is no prior month to subtract. For all subsequent months, the previous month's YTD is subtracted from the current month's YTD.

The function requires a Calendar table with a **YearMonth** column and a **Month number** column.

---

## Function

```DAX
EG.YTDDeAccumulate.Compute (
    <valueExpr>,            -- e.g., [Sales Amount]
    <YearMonthColumn>,      -- e.g., 'Date'[YearMonth]
    <MonthNumberColumn>,    -- e.g., 'Date'[Month]
    <DateColumn>            -- e.g., 'Date'[Date]
)
```

**Return:** The monthly (period) value for each month in the current filter context.

---

## Model requirements

1. **YTD fact table:** A table with at least a date column and a cumulative value column.

   ```DAX
   SalesYTD = 
   DATATABLE (
       "Date",  DATETIME,
       "Sales", INTEGER,
       {
           { dt"2026-1-1",  100  },
           { dt"2026-2-1",  250  },
           { dt"2026-3-1",  400  },
           { dt"2026-4-1",  550  },
           { dt"2026-5-1",  700  },
           { dt"2026-6-1",  900  },
           { dt"2026-7-1",  1100 },
           { dt"2026-8-1",  1300 },
           { dt"2026-9-1",  1500 },
           { dt"2026-10-1", 1800 }
       }
   )
   ```

2. **Calendar table:** Must contain at least three columns:
   - `Date` — one row per day, linked to the fact table
   - `YearMonth` — uniquely identifies the month; can be numeric (`202601`), string (`"2026-01"`), or a Start of Month date. The important requirement is that it carries **both the year and the month**
   - `Month` — month number from 1 to 12

3. **`Has Value` column (recommended):** The function iterates over all months in the current Calendar filter context. If the Calendar extends beyond the last month with data, months without values will produce negative results — because the function subtracts a previous-month YTD from a BLANK current-month value. Adding a boolean column to the Calendar table that marks only the months with actual data prevents this.

   ```DAX
   Has Value = 'Date'[Date] <= MAX ( SalesYTD[Date] )
   ```

---

## Basic usage

```DAX
MTD Sales Amount = 
CALCULATE (
    EG.YTDDeAccumulate.Compute (
        [Sales Amount],
        'Date'[YearMonth],
        'Date'[Month],
        'Date'[Date]
    ),
    'Date'[Has Value] = TRUE ()
)
```

Result with the `SalesYTD` sample data:

| Month    | YTD Sales | MTD Sales Amount |
|----------|-----------|-----------------|
| Jan 2026 | 100       | 100             |
| Feb 2026 | 250       | 150             |
| Mar 2026 | 400       | 150             |
| Apr 2026 | 550       | 150             |
| May 2026 | 700       | 150             |
| Jun 2026 | 900       | 200             |
| Jul 2026 | 1,100     | 200             |
| Aug 2026 | 1,300     | 200             |
| Sep 2026 | 1,500     | 200             |
| Oct 2026 | 1,800     | 300             |
