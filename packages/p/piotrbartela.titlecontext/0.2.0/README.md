# PiotrBartela.TitleContext

A set of two functions designed to add clear business context directly to visuals:

- `.Period` generates a presentation-style title based on the filtered date range
- `.Filtered` builds a dynamic subtitle describing the current filters.

This helps users instantly understand “what they are looking at” without checking filter panes or slicers (and potentialy asking about "export to Excel" button).

## Function 1: PiotrBartela.TitleContext.Period

Generates a concise, presentation-style text interpretation of a filtered date range.
The table below shows the detection logic and exact output text based on the function code.

- **One Calendar Year** → `2023` (or `Last Year (2023)` if the range covers the previous year)
- **Multiple Calendar Years** → `2021 - 2023 (3 Years)` or `Last 3 Years (2021 - 2023)`
- **One Calendar Quarter** → `2023 Qtr 4` or `Last Quarter (2023 Qtr 4)`
- **Multiple Quarters (Same Year)** → `2023 Qtr 2 - Qtr 3 (2 Qtrs)` or `Last 2Q (2023 Qtr 2 - Qtr 3)`
- **Multiple Quarters (Across Years)** → `Qtr 4'23 - Qtr 2'24 (3 Qtrs)` or `Last 3Q (Qtr 4'23 - Qtr 2'24)`
- **One Calendar Month** → `2024 June` or `Last Month (2024 June)`
- **Multiple Months (Same Year)** → `2024 April - June (3 Mts)` or `Last 3 Mts (2024 April - June)`
- **Multiple Months (Across Years)** → `2023 Oct - 2024 Mar (6 Mts)` or `Last 6 Mts (Oct'23 - Mar'24)`
- **Year-to-Date (YTD)** → `YTD 2024 up to June 30` or `Current YTD (2024) up to June 30`
- **Quarter-to-Date (QTD)** → `QTD Q'24 up to May 15` or `Current QTD (Q'24) up to May 15`
- **Month-to-Date (MTD)** → `MTD 2024 June 20` or `Current MTD 2024 June 20`
- **Single Day** → `2024 June 15` or `Today (2024 June 15)` / `Yesterday (...)`
- **Multiple Days (Same Month)** → `2024 June 1 - 15 (15 Days)` or `Last N Days (start - end)`
- **Multiple Days (Same Year)** → `2024 Mar 10` - `May 12 (64 Days)`
- **Multi-Year or Irregular Range** → `2023-11-20 - 2024-02-10 (X Days)` or `Last X Days (start - end)`

**Additional notes**
- If MinDate is BLANK(), the function returns: `Choose correct timeframe`.
- `Title in multiple periods (N days total)` - meaning filtered period isn't continuous.
- Many outputs have “Current / Last” variants, determined by the relationship of MaxDate to TODAY().

**Parameters**
PiotrBartela.TitleContext.Period(
  Title:string,
  DateCol:anyref
)

- **Title** - Prefix before the title like "Revenue" for `Revenue 2025` output.
- **DateCol** - Date column like `'Calendar'[Date]`.

**Example**
```dax
Title = PiotrBartela.TitleContext.Period(
    "Revenue",
    'Calendar'[Date]
)
```
---

## Function 2: PiotrBartela.TitleContext.Filtered

Generate a compact subtitle that describes the currently active filters and adjust its length so it does not exceed the given character limit.

**Key points**
- Supports up to 5 pairs (Name, Column) due to the current DAX UDF limit of 12 parameters (this limit is expected to expand in future updates).
- Each filter produces several versions of the text:
    - Full values list → Name: val1, val2, ...
    - Shortened forms (up to 3 values + “(+N)” if more)
    - Count-only form → Name (N)
- The function picks the longest possible version that fits within the MaxLength limit.
- If nothing fits, it falls back to: `Total Filters Applied: X`.

**Example outputs**
- **No filters** → `All Sales, Globally`
- **One filter** → `Region: Europe`
- **Two filters (shortened)** → `Region: Europe, Asia | Product: A, B (+3)`
- **Very short MaxLength** → `Total Filters Applied: 7`

**Parameters**
PiotrBartela.TitleContext.Filtered(
  MaxLength:int64,
  TestMode:boolean,
  Name1:string, Col1:anyref,
  Name2:string, Col2:anyref,
  Name3:string, Col3:anyref,
  Name4:string, Col4:anyref,
  Name5:string, Col5:anyref
)

- **MaxLength** - target maximum string length; the function automatically fits the output.
- **TestMode** - when TRUE, returns diagnostic text for debugging length (every number is displayed 10 times).
- **NameX / ColX** - label and column for each filter (pass a dummy column if not applicable, since anyref can’t be blank).

**Example**
```dax
Subtitle = PiotrBartela.TitleContext.Filtered(
    70,0,
    "First filter name",'Table1'[Column1],
    "",'Dummy'[Dummy],
    "",'Dummy'[Dummy],
    "",'Dummy'[Dummy],
    "",'Dummy'[Dummy]
)
```
---

## Potential updates
- Optimization
- More tables handeled in `.Filtered` when possible
- Relative future handeled in `.Period`

---

## Author

Piotr Bartela
[LinkedIn](https://www.linkedin.com/in/bartela-piotr/)

---

## License

MIT License.
