# EdwardCharles.WaterfallSVG

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Edward%20Charles-blue?style=flat&logo=linkedin)](https://www.linkedin.com/in/edward-charles-085025b1/)
[![YouTube](https://img.shields.io/badge/YouTube-@DropMaterializedView-red?style=flat&logo=youtube)](https://www.youtube.com/@DropMaterializedView)

Generate SVG waterfall charts for P&L visualization in Power BI.

![Example Chart](example.png)

## Attribution

Based on [IBCS® Chart Template C12: Vertical waterfalls](https://www.ibcs.com/resource/chart-template-12/)  
© 2024 IBCS Association | [www.ibcs.com](https://www.ibcs.com) with adaptations by Edward Charles.  
Licensed under [CC BY-SA 4.0 International](http://creativecommons.org/licenses/by-sa/4.0/legalcode).

## Usage

```dax
EVALUATE { 'EdwardCharles.WaterfallSVG.WaterfallChart'(WaterfallLayout, 200000, "2025 P&L", "USD Thousands") }
```

## Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `_dataTable` | TABLE | Layout table with hierarchy and values |
| `_maxValue` | INT64 | Scale maximum for bar width |
| `_Title` | STRING | Main chart title |
| `_Subtitle` | STRING | Subtitle text |

## Required Table Schema

| Column | Type | Description |
|--------|------|-------------|
| Key | INTEGER | Unique account identifier |
| Parent | INTEGER | Parent account key (BLANK for root) |
| Name | STRING | Display name |
| Type | STRING | "Expense", "Income", or "Subtotal" |
| Sort | INTEGER | Row ordering (ascending) |
| Depth | INTEGER | Hierarchy depth (1 = root) |
| IsLeaf | BOOLEAN | TRUE if no children |
| Path | STRING | DAX PATH() result |
| Value | DECIMAL | Raw value |
| Sign | INTEGER | +1 or -1 (calculated from AccountType) |

> **Note**: The UDF calculates `SignedValue = Value × Sign` internally.

## Creating the Layout Table

This follows the [DAX Patterns: Parent-Child Hierarchies](https://www.daxpatterns.com/parent-child-hierarchies/) approach.

### Step 1: Base Data Table

```dax
WaterfallLayout = 
DATATABLE(
    "Key", INTEGER,
    "Parent", INTEGER,
    "Name", STRING,
    "Type", STRING,
    "Value", DECIMAL,
    "Sort", INTEGER,
    {
        {1, BLANK(), "Gross Profit", "subtotal", BLANK(), 3},
        {2, 1, "Revenue", "Income", 172052, 1}, 
        {3, 1, "Cost of Goods Sold", "Expense", 61939, 2},
        {5, 4, "Salaries and Wages", "Expense", 14615, 4},
        {6, 4, "Bonuses", "Expense", 4406, 5},
        {7, 4, "Health Insurance", "Expense", 7318, 6},
        {8, 4, "Other", "Expense", 2910, 7},
        {4, 9, "Salaries and Wages", "subtotal", BLANK(), 8},
        {10, 9, "Rent and Overhead", "Expense", 10000, 9},
        {11, 9, "Depreciation and Amortization", "Expense", 15003, 10},
        {12, 9, "Interest", "Expense", 500, 11},
        {9, 13,"Expenses", "subtotal", BLANK(), 12},
        {13, BLANK(),"Earnings Before Tax", "subtotal", BLANK(), 13},
        {14, BLANK(),"Taxes", "Expense", 15501, 14}
    }
)
```

### Step 2: Add Calculated Columns

```dax
// Path: Hierarchy path using DAX PATH function
Path = PATH(WaterfallLayout[Key], WaterfallLayout[Parent])

// Depth: Hierarchy level
Depth = PATHLENGTH(WaterfallLayout[Path])

// IsLeaf: TRUE if no children exist
IsLeaf = NOT(
    CALCULATE(
        COUNTROWS(WaterfallLayout), 
        FILTER(ALL(WaterfallLayout), WaterfallLayout[Parent] = EARLIER(WaterfallLayout[Key]))
    ) > 0
)

// Sign: -1 for Expenses, +1 for everything else
Sign = IF(WaterfallLayout[Type] IN {"Expense"}, -1, 1)
```

### Step 3: Call the UDF

Since the table columns match the required schema, you can pass the table directly:

```dax
WaterfallChart = 'EdwardCharles.WaterfallSVG.WaterfallChart'(
    WaterfallLayout,
    200000,
    "2025 Profit & Loss",
    "USD ($)"
)
```

## Output

Returns an SVG data URI string for use in Power BI Image visual.

## License

This work is licensed under [CC BY-SA 4.0 International](http://creativecommons.org/licenses/by-sa/4.0/).
