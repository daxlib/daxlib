# XU.TMDL.TimeIntelligence

A UDF library for **automatically generating TMDL scripts** that create comprehensive time intelligence measures in Power BI — eliminating manual, error-prone measure creation.

Based on your existing base measures, this library generates scripts to create 30+ time intelligence calculations including YTD, QTD, MTD, YOY, YOYTD, MAT, and period-over-period comparisons — all with consistent formatting and logic.

---

## What it does

The `XU.TMDL.TimeIntelligence` package provides a streamlined workflow for time intelligence measure creation:

1. **Define** your base measures (e.g., "Sales Amount", "Profit")
2. **Generate** a complete TMDL script with one function call
3. **Execute** the script to create all time intelligence measures at once

This approach ensures:

- **Consistent logic** across all time calculations
- **Reduced development time** (minutes instead of hours)
- **Fewer errors** compared to manual measure creation
- **Easy maintenance** when business rules change

---

## Usage Workflow

Follow these steps to generate and deploy time intelligence measures:

1. **Verify prerequisites**:

   - Ensure your base measures already exist (e.g., "Sales Amount", "Profit")
   - Confirm you have a proper date table in your model
   - Make sure the target table for new measures exists in your model
2. **Create the generator measure**:

   ```dax
   TMDL Script = 
   XU.TMDL.TimeIntelligence.MeasureCreate(
       {"Sales Amount", "Sales Cost"},  // Your existing base measures
       "'Calendar'[Date]",                          // Your date column reference
       "'Financial Metrics'"                      // Table to store new measures
   )
   ```
3. **Place the measure in a table visual**:

   - Create a new table visual on your report page
   - Add the "TMDL Script" measure to the Values field well
   - The visual will display the complete TMDL script
4. **Copy the generated script**:
   ![XU.TMDL.TimeIntelligence_CopyScript.png](https://raw.githubusercontent.com/DS-Vic/PIC/main/XU.TMDL.TimeIntelligence_CopyScript.png)
5. **Execute the TMDL script**:

   - Paste the script (Ctrl+V)
   - Execute the script to create all 30+ time intelligence measures automatically
     ![XU.TMDL.TimeIntelligence_ExecuteScript.png](https://raw.githubusercontent.com/DS-Vic/PIC/main/XU.TMDL.TimeIntelligence_ExecuteScript.png)

> 💡 **Tip**: Always review the generated script before execution, especially in production environments. Start with a small set of base measures to validate the process before scaling up.

---

## Functions

### `XU.TMDL.TimeIntelligence.MeasureCreate(MeasureList, DateColumn, MeasureTable)`

The core function that generates a complete TMDL script to create time intelligence measures for multiple base measures.

**Parameters**:

- `MeasureList`: List of base measure names (e.g., `{"Sales Amount", "Profit"}`)
- `DateColumn`: Date column name (e.g., `"'Calendar'[Date]"`)
- `MeasureTable`: Name of table where the generated measures will be created (e.g., `"Financial Metrics"`)

**Returns**: A complete TMDL script ready for execution.

```dax
TMDL Script = 
XU.TMDL.TimeIntelligence.MeasureCreate(
    {"Sales Amount", "Sales Cost"},
    "'Calendar'[Date]",
    "'Financial Metrics'"
)
```

---

### `XU.TMDL.TimeIntelligence.About()`

Returns a formatted string with library metadata (version, author, functions, usage instructions).

Useful for documentation or auditing installed packages.

```dax
EVALUATE { XU.TMDL.TimeIntelligence.About() }
```

---

## Global Configuration

The library includes two configuration functions to control the formatting of generated measures:

### `XU.TMDL.TimeIntelligence.Config.NumberFormat()`

Defines the default format string for numeric measures (e.g., YTD, PY, YOY).

**Default**: `"#,0"` (thousands separator, no decimals)

**Example override**:

```dax
FUNCTION 'XU.TMDL.TimeIntelligence.Config.NumberFormat' = () => "#,0.00"
```

### `XU.TMDL.TimeIntelligence.Config.PercentFormat()`

Defines the default format string for percentage measures (e.g., YOY %, MOM %).

**Default**: `"#,0.00%;-#,0.00%;#,0.00%"` (positive/negative/zero formats)

**Example override**:

```dax
FUNCTION 'XU.TMDL.TimeIntelligence.Config.PercentFormat' = () => "0.0%"
```

> 💡 **Tip**: Override these configuration functions in your model to apply consistent formatting across all generated measures.

---

## Generated Measures

For each base measure, the library generates 34 time intelligence variations:

### Period-to-Date

- **YTD** (Year-to-Date)
- **QTD** (Quarter-to-Date)
- **MTD** (Month-to-Date)

### Previous Period

- **PY** (Previous Year)
- **PQ** (Previous Quarter)
- **PM** (Previous Month)
- **PYC** (Previous Year Complete)
- **PQC** (Previous Quarter Complete)
- **PMC** (Previous Month Complete)

### Period-over-Period

- **YOY** (Year-over-Year absolute)
- **QOQ** (Quarter-over-Quarter absolute)
- **MOM** (Month-over-Month absolute)
- **YOY %** (Year-over-Year percentage)
- **QOQ %** (Quarter-over-Quarter percentage)
- **MOM %** (Month-over-Month percentage)

### Previous Period-to-Date

- **PYTD** (Previous Year-to-Date)
- **PQTD** (Previous Quarter-to-Date)
- **PMTD** (Previous Month-to-Date)

### Period-to-Date Comparisons

- **YOYTD** (Year-over-Year-to-Date absolute)
- **QOQTD** (Quarter-over-Quarter-to-Date absolute)
- **MOMTD** (Month-over-Month-to-Date absolute)
- **YOYTD %** (Year-over-Year-to-Date percentage)
- **QOQTD %** (Quarter-over-Quarter-to-Date percentage)
- **MOMTD %** (Month-over-Month-to-Date percentage)

### Period-to-Date vs Complete Period

- **YTDOPYC** (YTD Over Previous Year Complete)
- **QTDOPQC** (QTD Over Previous Quarter Complete)
- **MTDOPMC** (MTD Over Previous Month Complete)
- **YTDOPYC %** (YTD Over Previous Year Complete percentage)
- **QTDOPQC %** (QTD Over Previous Quarter Complete percentage)
- **MTDOPMC %** (MTD Over Previous Month Complete percentage)

### Moving Annual Total

- **MAT** (Moving Annual Total)
- **PYMAT** (Previous Year Moving Annual Total)
- **MATG** (MAT Growth absolute)
- **MATG %** (MAT Growth percentage)

---

## License

MIT License - free to use, modify, and distribute with proper attribution.

© HD XU - For support or contributions, contact the author.
