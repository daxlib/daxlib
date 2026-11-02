# SavoryData.Selection2List

A DAX library for displaying selected values as readable text in Power BI.

`SavoryData.Selection2List` provides reusable DAX functions that turn the values available in the current filter context into concise, human-readable lists. It is useful for making slicer selections and filter context visible in report titles, subtitles, buttons, cards, and other text-based report elements.

The library supports text columns and date dimensions with year, quarter, month, week, and date levels. It also includes helper functions for inspecting minimum and maximum values and counting rows or values.

## Features

- Display selected text values as readable lists.
- Limit the number of text values displayed.
- Provide a custom label when no filter is applied.
- Summarize date selections at date, month, quarter, week, or year level.
- Condense consecutive date or period selections into a range.
- Configure the default date format.
- Inspect minimum and maximum values and counts.
- Reuse the functions in DAX measures in Power BI semantic models.

## Typical Use Cases

- Displaying selected products, customers, or categories.
- Showing selected dates or reporting periods.
- Summarizing selected months, quarters, weeks, or years.
- Creating dynamic report titles and subtitles.
- Making the active filter context visible to report users.
- Debugging the values available in a filter context.

## Configuration Functions

| Function | Default | Description |
|---|---|---|
| `SavoryData.Selection2List.Config_MaxNumberOfElements` | `3` | Default maximum number of text values shown by `Text2List` when no explicit limit is supplied. |
| `SavoryData.Selection2List.Config_TextColumnName` | `BLANK()` | Default label used by `Text2List` when the column is not cross-filtered. |
| `SavoryData.Selection2List.Config_DateFormat` | `"YYYY-MM-DD"` | Format string passed to `FORMAT` for dates returned by `Date2List`, `YQMD2List`, and `YWD2List`. |

These functions take no parameters. To change a default, edit the corresponding configuration function in the library.

## Functions

### Text2List

**Function:** `SavoryData.Selection2List.Text2List`

**Purpose:** Returns the values of a text column in the current filter context as a readable list.

**Signature:**

```dax
SavoryData.Selection2List.Text2List(
    pTextColumn,
    pMaxNumberOfElements = 3,
    pAllTextColumnName = BLANK()
)
```

**Parameters:**

| Parameter | Type in TMDL | Required? | Description and typical values |
|---|---|---:|---|
| `pTextColumn` | `COLUMNREF EXPR` | Yes | The column whose available values should be listed. Typically a descriptive text column such as `'Product'[Product Name]`, `'Customer'[Country]`, or `'Product'[Category]`. |
| `pMaxNumberOfElements` | `SCALAR INT64` | No | Maximum number of values to display when the selection exceeds the limit. Defaults to `3`. Typical values are `3`, `5`, or `10`. If omitted, the function signature supplies `3`; the lower-level functions also use the configured default when passed a blank value. |
| `pAllTextColumnName` | `SCALAR STRING` | No | Label returned when the column is not cross-filtered. For example, `"All Products"` or `"All Countries"`. If omitted or blank, the function uses `Config_TextColumnName`; if that is also blank, it generates a label such as `All Product Name`. |

**Output behaviour:**

- If the column is not filtered, the function returns the all-values label.
- If one value is available, it returns that value.
- If two values are available, it joins them with ` & `, for example `A & B`.
- If the number of values is within the limit, it returns a comma-separated list.
- If the number of values exceeds the limit, it returns the first *N* values followed by `, ...`.

The values are obtained from `VALUES(pTextColumn)`. The function does not define a custom sort order; the order follows the DAX table expression used by the implementation.

**Examples:**

Use the defaults (maximum three values and generated/configured all-values label):

```dax
Selected Products =
    SavoryData.Selection2List.Text2List(
        'Product'[Product Name]
    )
```

Show up to five products and provide a custom label for the unfiltered state:

```dax
Selected Products =
    SavoryData.Selection2List.Text2List(
        'Product'[Product Name],
        5,
        "All Products"
    )
```

Use the overload that accepts only a maximum number of elements:

```dax
Selected Countries =
    SavoryData.Selection2List.Text2List_MaxNumberOfElements(
        'Geography'[Country],
        4
    )
```

Use the overload that accepts both optional settings explicitly:

```dax
Selected Brands =
    SavoryData.Selection2List.Text2List_MaxNumberOfElements_TextColumnName(
        'Product'[Brand],
        6,
        "All Brands"
    )
```

### Date2List

**Function:** `SavoryData.Selection2List.Date2List`

**Purpose:** Returns a concise representation of the selected dates, months, or years. The function chooses the most detailed level that represents a partial selection in the current filter context.

**Signature:**

```dax
SavoryData.Selection2List.Date2List(
    pYear,
    pMonth,
    pDate
)
```

**Parameters:**

| Parameter | Type in TMDL | Required? | Description and typical values |
|---|---|---:|---|
| `pYear` | `COLUMNREF EXPR` | Yes | Year column from the date dimension, for example `'Date'[Year]` containing values such as `2025` and `2026`. |
| `pMonth` | `COLUMNREF EXPR` | Yes | Month-level column from the same date dimension, for example `'Date'[YearMonth]` or a model-specific month key. Use a value that represents the month level consistently in your date model. |
| `pDate` | `COLUMNREF EXPR` | Yes | Date column from the same date dimension, typically `'Date'[Date]` with one row per calendar date. |

**Output behaviour:**

- One selected date is returned using `Config_DateFormat`.
- Two selected dates are joined with ` & `.
- Three or more consecutive selected dates can be represented by the first and last date separated by ` - `.
- Arbitrary selected dates are delegated to `Text2List`.
- If the selection is better represented at month level, the function applies the equivalent single-value, two-value, consecutive-range, or list behaviour to the month column.
- If the selection is represented at year level, one or two years are returned directly; all years return `All Years`; consecutive years are returned as a range; other year selections are listed through `Text2List`.

**Example:**

```dax
Selected Period =
    SavoryData.Selection2List.Date2List(
        'Date'[Year],
        'Date'[YearMonth],
        'Date'[Date]
    )
```

For example, a selection of consecutive dates may be displayed as `2026-01-01 - 2026-01-05`, depending on the selected dates and the configured date format.

### YQMD2List

**Function:** `SavoryData.Selection2List.YQMD2List`

**Purpose:** Extends the date summarization logic to include a quarter level. It returns a concise representation of the selected dates, months, quarters, or years.

**Signature:**

```dax
SavoryData.Selection2List.YQMD2List(
    pYear,
    pQuarter,
    pMonth,
    pDate
)
```

**Parameters:**

| Parameter | Type in TMDL | Required? | Description and typical values |
|---|---|---:|---|
| `pYear` | `COLUMNREF EXPR` | Yes | Year column, for example `'Date'[Year]` with values such as `2025` and `2026`. |
| `pQuarter` | `COLUMNREF EXPR` | Yes | Quarter-level column from the date dimension, for example `'Date'[YearQuarter]` or the quarter key used by your model. |
| `pMonth` | `COLUMNREF EXPR` | Yes | Month-level column from the same date dimension, for example `'Date'[YearMonth]`. |
| `pDate` | `COLUMNREF EXPR` | Yes | Date column, typically `'Date'[Date]`. |

**Output behaviour:**

The function checks the selection from the most detailed level upwards: dates, months, quarters, and years. Single values are returned directly, two values are joined with ` & `, and consecutive selections can be summarized as a range. Arbitrary selections are passed to `Text2List`. If all years are selected, the result is `All Years`.

**Example:**

```dax
Selected Period =
    SavoryData.Selection2List.YQMD2List(
        'Date'[Year],
        'Date'[YearQuarter],
        'Date'[YearMonth],
        'Date'[Date]
    )
```

### YWD2List

**Function:** `SavoryData.Selection2List.YWD2List`

**Purpose:** Extends the date summarization logic to include a week level. It returns a concise representation of the selected dates, weeks, or years.

**Signature:**

```dax
SavoryData.Selection2List.YWD2List(
    pYear,
    pWeek,
    pDate
)
```

**Parameters:**

| Parameter | Type in TMDL | Required? | Description and typical values |
|---|---|---:|---|
| `pYear` | `COLUMNREF EXPR` | Yes | Year column, for example `'Date'[Year]` with values such as `2025` and `2026`. |
| `pWeek` | `COLUMNREF EXPR` | Yes | Week-level column from the date dimension, for example `'Date'[YearWeek]` or the week key used by your model. |
| `pDate` | `COLUMNREF EXPR` | Yes | Date column, typically `'Date'[Date]`. |

**Output behaviour:**

The function checks the selection from the most detailed level upwards: dates, weeks, and years. Single values are returned directly, two values are joined with ` & `, and consecutive selections can be summarized as a range. Arbitrary selections are passed to `Text2List`. If all years are selected, the result is `All Years`.

**Example:**

```dax
Selected Period =
    SavoryData.Selection2List.YWD2List(
        'Date'[Year],
        'Date'[YearWeek],
        'Date'[Date]
    )
```

### MinMaxCount

**Function:** `SavoryData.Selection2List.MinMaxCount`

**Purpose:** Returns the minimum and maximum values of a column, converted to text, together with the number of rows in a supplied table.

**Signature:**

```dax
SavoryData.Selection2List.MinMaxCount(
    pDateTable,
    pColumn
)
```

**Parameters:**

| Parameter | Type in TMDL | Required? | Description and typical values |
|---|---|---:|---|
| `pDateTable` | `TABLE VAL` | Yes | A table expression containing the rows to inspect. For example, `VALUES('Date'[Date])`, `ALLSELECTED('Date')`, or another table expression appropriate to the measure. Despite the parameter name, the implementation can be used with a table other than a date table. |
| `pColumn` | `COLUMNREF EXPR` | Yes | The column whose minimum and maximum values should be calculated. Typically a column belonging to the table supplied as `pDateTable`. |

**Return format:**

```text
<minimum> - <maximum> (<row count>)
```

The minimum and maximum are converted to text. If the supplied table has no rows, the function returns blank.

**Example:**

```dax
Available Date Range =
    SavoryData.Selection2List.MinMaxCount(
        VALUES('Date'[Date]),
        'Date'[Date]
    )
```

### MinMaxCountDate

**Function:** `SavoryData.Selection2List.MinMaxCountDate`

**Purpose:** Returns the minimum and maximum values of a supplied column as dates, followed by the number of rows in the supplied table.

**Signature:**

```dax
SavoryData.Selection2List.MinMaxCountDate(
    pDateTable,
    pColumn
)
```

**Parameters:**

| Parameter | Type in TMDL | Required? | Description and typical values |
|---|---|---:|---|
| `pDateTable` | `TABLE VAL` | Yes | A table expression containing the date rows to inspect, for example `VALUES('Date'[Date])` or `ALLSELECTED('Date')`. |
| `pColumn` | `COLUMNREF EXPR` | Yes | The date column to use for the minimum and maximum calculations, typically `'Date'[Date]`. |

**Return format:**

```text
YYYY-MM-DD - YYYY-MM-DD (<row count>)
```

This function uses the fixed format `YYYY-MM-DD` in its implementation. It does not use the configurable `Config_DateFormat` function. If the supplied table has no rows, the result is blank.

**Example:**

```dax
Available Date Range =
    SavoryData.Selection2List.MinMaxCountDate(
        VALUES('Date'[Date]),
        'Date'[Date]
    )
```

### MinMaxCountV2

**Function:** `SavoryData.Selection2List.MinMaxCountV2`

**Purpose:** Returns the minimum and maximum values of a column, converted to text, together with the number of rows returned by `TABLEOF` for that column. Unlike `MinMaxCount` and `MinMaxCountDate`, it does not require a table expression as an argument.

**Signature:**

```dax
SavoryData.Selection2List.MinMaxCountV2(
    pColumn
)
```

**Parameters:**

| Parameter | Type in TMDL | Required? | Description and typical values |
|---|---|---:|---|
| `pColumn` | `COLUMNREF EXPR` | Yes | The column to inspect, for example `'Product'[Product Name]`, `'Date'[Date]`, or `'Geography'[Country]`. |

**Return format:**

```text
<minimum> - <maximum> (<count>)
```

The count is the number of rows in `TABLEOF(pColumn)`. If that table has no rows, the function returns blank.

**Example:**

```dax
Available Product Range =
    SavoryData.Selection2List.MinMaxCountV2(
        'Product'[Product Name]
    )
```

## Parameter and Model Notes

- Parameters marked `COLUMNREF EXPR` must be supplied as column references, such as `'Product'[Product Name]`, rather than as text strings containing a column name.
- The date functions expect the supplied year, quarter, month, week, and date columns to describe levels of the same date dimension.
- For period columns used in range checks, use values whose ordering and comparisons make sense for the period sequence. The TMDL implementation compares the minimum and maximum values to detect consecutive ranges.
- The date functions use the date format configured by `Config_DateFormat`; `MinMaxCountDate` instead uses its own fixed `YYYY-MM-DD` format.
- `Text2List` uses the current filter context and `ISCROSSFILTERED` to determine whether its column is cross-filtered. Its unfiltered label is not a count of selected values.

## Power BI Usage

The functions can be used in regular DAX measures. Add the resulting measure to a card, title, subtitle, button, or other visual that supports dynamic text.

### Dynamic Report Titles

Include the current selection in a report title, for example:

```dax
Report Title =
    "Sales for " &
    SavoryData.Selection2List.Text2List(
        'Geography'[Country],
        3,
        "All Countries"
    )
```

### Dynamic Subtitles

Show the selected reporting period below a report title:

```dax
Report Subtitle =
    "Period: " &
    SavoryData.Selection2List.Date2List(
        'Date'[Year],
        'Date'[YearMonth],
        'Date'[Date]
    )
```

### Filter Context Indicators

Make slicer selections visible to report users by placing a `Text2List`, `Date2List`, `YQMD2List`, or `YWD2List` measure in a card or text element.

### Debugging

Use `MinMaxCount`, `MinMaxCountDate`, or `MinMaxCountV2` to inspect the range and number of values available in the current context.

## Documentation

See the `manifest.daxlib` and `lib/functions.tmdl` files for package metadata, function definitions, parameter types, and implementation details.

## Contributing

Contributions are welcome. Please open an issue or submit a pull request in the project repository.

## License

This project is licensed under the MIT License.
