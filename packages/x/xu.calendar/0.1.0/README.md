# XU.Calendar

A DAX user-defined function (UDF) library for generating robust, customizable **date dimension tables** in Power BI.  
Supports calendar, fiscal year (configurable start month), and ISO week attributes — with variants for different modeling needs.

## What it does

The `XU.Calendar` package provides three ready-to-use functions to create date tables:

- **Full-featured**: Includes calendar, fiscal, and ISO week attributes.
- **No fiscal**: Same as full but excludes all fiscal-related columns.
- **Basic**: Minimal set of essential date attributes for lightweight models.

All functions return a table with a `[Date]` column that can be used as a proper date table in Power BI — **after manually marking it as a date table**.

These functions eliminate repetitive date table logic across models and ensure consistency in time intelligence setups.

---

## Functions

### `XU.Calendar.Build (StartDate, EndDate, FiscalYearStartMonth)`

Generates a comprehensive date dimension table with:
- Standard calendar hierarchy (Year, Quarter, Month, Day)
- Fiscal year attributes (configurable start month, e.g., July = 7)
- ISO week and ISO year (based on ISO 8601)
- Time indicators (`Is Weekend`, `Is Work Day`)
- Sort keys (`DateKey`, `YearMonthSort`, etc.)

**Parameters**
- `StartDate` (`DATETIME`): First date in the calendar (e.g., `DATE(2015, 1, 1)`)
- `EndDate` (`DATETIME`): Last date in the calendar (e.g., `DATE(2030, 12, 31)`)
- `FiscalYearStartMonth` (`INT64`): Month number (1–12) when fiscal year begins (e.g., `7` for July)

---

### `XU.Calendar.Build.NoFiscal (StartDate, EndDate)`

Same as `Build`, but **excludes all fiscal year columns** (`Fiscal Year`, `Fiscal Quarter`, etc.).  
Ideal for organizations that don’t use fiscal calendars or want to reduce table width.

**Parameters**
- `StartDate` (`DATETIME`)
- `EndDate` (`DATETIME`)

---

### `XU.Calendar.Build.Basic (StartDate, EndDate)`

Returns a **minimal date table** with only core attributes:
- `Year`, `Quarter`, `Month`, `Day of Week`
- `Is Weekend`, `Is Work Day`
- Basic sort keys

Perfect for simple reports or when performance and model size are critical.

**Parameters**
- `StartDate` (`DATETIME`)
- `EndDate` (`DATETIME`)

---

### `XU.Calendar.About ()`

Returns a formatted string with library metadata (version, author, functions, usage note).  
Useful for documentation or auditing installed packages.

```dax
EVALUATE { XU.Calendar.About() }
```

---

## Important Note

> ⚠️ **After creating a date table using any of these functions, you must manually mark it as a date table in Power BI**:  
> **Table Tools** → (Calendar Options) → **Mark as date table** → Select the `[Date]` column.



---

## Basic Usage

Create a new calculated table in Power BI:

```dax
Date = XU.Calendar.Build(
    DATE(2015, 1, 1),
    DATE(2030, 12, 31),
    7  // Fiscal year starts in July
)
```

Or use the lightweight version:

```dax
Date = XU.Calendar.Build.Basic(
    DATE(2020, 1, 1),
    DATE(2025, 12, 31)
)
```

Then **mark the table as a date table** (see above).

---

## Model Requirements

- Power BI **September 2025 or later** (required for DAX UDF support).
- No external dependencies — all logic is self-contained.
- Date range must be reasonable (e.g., 10–20 years); extremely large ranges may impact performance.

---

## Tips & Best Practices

- **Use `VAL` parameters**: All functions use `DATETIME VAL` and `INT64 VAL`, ensuring stable evaluation and avoiding context-related side effects.
- **Avoid duplication**: Use only one date table per model.
- **Customize later**: You can add holidays or working-day logic in a separate calculated column if needed.
- **Version control**: Check `XU.Calendar.About()` to verify the library version in your model.

---

## License

MIT License.

---