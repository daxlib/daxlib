# Tharun.FormatString v0.2.0 - Usage Guide

This package provides advanced DAX UDF functions for creating dynamic, customizable number format strings. Perfect for building flexible dashboards and reports that adapt to different data scales and display requirements.

## Functions Overview

### 1. Tharun.FormatString.GetDynamicFormatString
Creates dynamic format strings with comprehensive customization options including thousand separators, decimal places, negative number brackets, thousands multipliers, and delta indicators.

**Parameters:**
- `Val` (anyref): The numeric value to determine scaling (K, M, B, T)
- `numberOfDecimalPlaces` (numeric): Number of decimal places to display (0, 1, 2, etc.)
- `thousandSeperator` (boolean): If TRUE, adds commas as thousand separators
- `multiplesOfThousand` (boolean): If TRUE, displays values as K (thousands), M (millions), B (billions), T (trillions)
- `negativeNumbersBracket` (boolean): If TRUE, negative numbers display in brackets like (1,234); if FALSE, use minus sign
- `truncateZero` (boolean): If TRUE, hides zero values; if FALSE, displays them
- `deltaIcon` (boolean): If TRUE, adds ▲ for positive and ▼ for negative values

**Usage Examples:**

| Input | Separators | Multiplex | Brackets | Delta | Decimals | Output |
|-------|-----------|----------|----------|-------|----------|--------|
| 1000000 | FALSE | TRUE | FALSE | TRUE | 1 | "1.0M▲" |
| 1000000 | TRUE | TRUE | FALSE | TRUE | 1 | "1,000.0K▲" |
| -1000000 | FALSE | TRUE | TRUE | TRUE | 1 | "(1.0M)▼" |
| 5000 | FALSE | FALSE | FALSE | FALSE | 2 | "5000.00" |
| 5000 | TRUE | FALSE | FALSE | FALSE | 2 | "5,000.00" |
| 1500000000 | FALSE | TRUE | FALSE | TRUE | 2 | "1.50B▲" |
| 1500000000 | TRUE | TRUE | TRUE | TRUE | 2 | "(1,500.00M)▼" |

**DAX Example:**
```dax
// Sales metric with automatic scaling
FORMAT(1250000, Tharun.FormatString.GetDynamicFormatString(1250000, 1, FALSE(), TRUE(), FALSE(), FALSE(), TRUE()))
// Returns: "1.3M▲"

// Revenue with thousand separators and brackets for negatives
FORMAT(-750000, Tharun.FormatString.GetDynamicFormatString(-750000, 0, TRUE(), TRUE(), TRUE(), FALSE(), FALSE()))
// Returns: "(750K)"

// Budget comparison with delta icons
FORMAT(2500000, Tharun.FormatString.GetDynamicFormatString(2500000, 2, FALSE(), TRUE(), FALSE(), TRUE(), TRUE()))
// Returns: "2.50M▲"
```

---

### 2. Tharun.FormatString.GetNumericDynamicFormatString
Creates numeric format strings with custom delta icons for more control over visual indicators. Ideal for dashboards that need custom positive/negative icons.

**Parameters:**
- `Val` (anyref): The numeric value to determine scaling
- `numberOfDecimalPlaces` (numeric): Number of decimal places
- `thousandSeperator` (boolean): Include comma separators
- `multiplesOfThousand` (boolean): Display as K/M/B/T
- `negativeNumbersBracket` (boolean): Use brackets for negatives
- `truncateZero` (boolean): Hide zero values
- `deltaIconRequired` (boolean): If TRUE, includes custom icons
- `preferredPositiveDeltaIcon` (string): Custom icon for positive values (e.g., "↑", "📈", "✓")
- `preferredNegativeDeltaIcon` (string): Custom icon for negative values (e.g., "↓", "📉", "✗")

**Usage Examples:**

| Input | Positive Icon | Negative Icon | Multiplex | Output |
|-------|---------------|---------------|-----------|--------|
| 2500000 | "↑" | "↓" | TRUE | "2.5M ↑" |
| -1500000 | "📈" | "📉" | TRUE | "(1.5M) 📉" |
| 1000 | "✓" | "✗" | FALSE | "1000 ✓" |
| -5000 | "▲" | "▼" | FALSE | "(5000) ▼" |
| 750000 | "📊" | "❌" | TRUE | "750K 📊" |

**DAX Example:**
```dax
// Growth indicator with custom icons
FORMAT(3200000, Tharun.FormatString.GetNumericDynamicFormatString(3200000, 1, FALSE(), TRUE(), FALSE(), FALSE(), TRUE(), "📈", "📉"))
// Returns: "3.2M 📈"

// Performance metric with checkmarks
FORMAT(-500000, Tharun.FormatString.GetNumericDynamicFormatString(-500000, 0, TRUE(), FALSE(), TRUE(), FALSE(), TRUE(), "✓", "✗"))
// Returns: "(500,000) ✗"

// Sales target with arrows
FORMAT(1200000, Tharun.FormatString.GetNumericDynamicFormatString(1200000, 2, FALSE(), TRUE(), FALSE(), TRUE(), TRUE(), "↑", "↓"))
// Returns: "1.20M ↑"
```

---

### 3. Tharun.FormatString.GetPercentageDynamicFormatString
Creates percentage format strings with custom delta icons. Perfect for KPI displays, target achievement metrics, and growth percentages.

**Parameters:**
- `Val` (anyref): The percentage value (as decimal, e.g., 0.85 for 85%)
- `numberOfDecimalPlaces` (numeric): Decimal places for percentage (0, 1, 2, etc.)
- `inputIsPercentage` (boolean): If TRUE, escapes % as \%; if FALSE, normal %
- `negativeNumbersBracket` (boolean): Use brackets for negative percentages
- `truncateZero` (boolean): Hide zero percentages
- `deltaIconRequired` (boolean): Include custom delta icons
- `preferredPositiveDeltaIcon` (string): Icon for positive change
- `preferredNegativeDeltaIcon` (string): Icon for negative change

**Usage Examples:**

| Input | Decimals | Icon Req. | Positive | Negative | Output |
|-------|----------|----------|----------|----------|--------|
| 0.95 | 0 | TRUE | "↑" | "↓" | "95% ↑" |
| 0.95 | 2 | TRUE | "📈" | "📉" | "95.00% 📈" |
| -0.15 | 1 | TRUE | "📊" | "❌" | "(15.0%) ❌" |
| 0.75 | 0 | FALSE | "✓" | "✗" | "75%" |
| -0.05 | 2 | TRUE | "▲" | "▼" | "(5.00%) ▼" |

**DAX Example:**
```dax
// Target achievement with arrow indicators
FORMAT(0.87, Tharun.FormatString.GetPercentageDynamicFormatString(0.87, 1, FALSE(), FALSE(), FALSE(), TRUE(), "↑", "↓"))
// Returns: "87.0% ↑"

// Variance with custom icons
FORMAT(-0.12, Tharun.FormatString.GetPercentageDynamicFormatString(-0.12, 2, FALSE(), TRUE(), FALSE(), TRUE(), "📈", "📉"))
// Returns: "(12.00%) 📉"

// Growth rate simple display
FORMAT(0.45, Tharun.FormatString.GetPercentageDynamicFormatString(0.45, 0, FALSE(), FALSE(), FALSE(), FALSE(), "", ""))
// Returns: "45%"
```

---

## Real-World Scenarios

### Scenario 1: Sales Dashboard with Automatic Scaling
Display sales figures that automatically scale based on magnitude.

```dax
// Sales Values: 500K, 5M, 15M
Sales_Formatted = 
    IF(Sales >= 1000000,
        FORMAT(Sales, Tharun.FormatString.GetDynamicFormatString(Sales, 1, FALSE(), TRUE(), FALSE(), FALSE(), TRUE())),
        FORMAT(Sales, Tharun.FormatString.GetDynamicFormatString(Sales, 0, TRUE(), FALSE(), FALSE(), FALSE(), FALSE()))
    )

// Results: "0.5M▲", "5.0M▲", "15.0M▲"
```

### Scenario 2: Budget Variance Report with Brackets and Delta Icons
Show budget variances with visual indicators.

```dax
// Variance: +250000, -500000, +1200000
Variance_Formatted = 
    FORMAT(Variance, 
        Tharun.FormatString.GetNumericDynamicFormatString(Variance, 0, TRUE(), TRUE(), TRUE(), FALSE(), TRUE(), "✓ Over", "✗ Under")
    )

// Results: "250K ✓ Over", "(500K) ✗ Under", "1.2M ✓ Over"
```

### Scenario 3: KPI Target Achievement Display
Show percentage achievement of targets with custom icons.

```dax
// Achievement: 92%, 75%, 110%
Achievement_Formatted = 
    FORMAT(Achievement, 
        Tharun.FormatString.GetPercentageDynamicFormatString(Achievement, 1, FALSE(), FALSE(), FALSE(), TRUE(), "📈", "📉")
    )

// Results: "92.0% 📈", "75.0% 📈", "110.0% 📈"
```

### Scenario 4: Multi-Currency Display with Negative Brackets
Display values across different currency scales consistently.

```dax
// EUR: 1200000, GBP: -750000, USD: 2500000000
Currency_Display = 
    FORMAT(Amount,
        Tharun.FormatString.GetDynamicFormatString(Amount, 2, TRUE(), TRUE(), TRUE(), FALSE(), FALSE())
    )

// Results: "1.20M", "(750K)", "2.50B"
```

### Scenario 5: Growth Metrics with Directional Indicators
Track growth with up/down visual cues.

```dax
// Growth: +18%, -3%, +5.5%
Growth_Formatted = 
    FORMAT(GrowthRate,
        Tharun.FormatString.GetPercentageDynamicFormatString(GrowthRate, 1, FALSE(), FALSE(), FALSE(), TRUE(), "↑", "↓")
    )

// Results: "18.0% ↑", "(3.0%) ↓", "5.5% ↑"
```

---

## Format Details

### Thousand Multiplier Scales
- **K** = 1,000 (thousands)
- **M** = 1,000,000 (millions)
- **B** = 1,000,000,000 (billions)
- **T** = 1,000,000,000,000 (trillions)

### Available Icons (Recommendations)
**Directional:**
- Positive: ↑, ▲, 📈, ↗
- Negative: ↓, ▼, 📉, ↘

**Status:**
- Success: ✓, ✔, ✅, 👍
- Failure: ✗, ✘, ❌, 👎

**Custom:**
- Percentage increase: ⬆, 📊, 💹
- Percentage decrease: ⬇, 📋, 📉

---

## Notes

- Use `Tharun.FormatString.GetDynamicFormatString` for standard numeric displays with built-in icons (▲/▼)
- Use `Tharun.FormatString.GetNumericDynamicFormatString` when you need custom icons for better visual control
- Use `Tharun.FormatString.GetPercentageDynamicFormatString` exclusively for percentage metrics
- Test formatting with your actual data ranges to ensure scaling works as expected
- All functions work with FORMAT() DAX function for rendering values
- Custom icons can be emojis, Unicode symbols, or plain text strings

---

## About the Author

For more insights on DAX, Power BI, and data analytics, checkout my website: **[techietips.co.in](https://techietips.co.in)** to read all my blogs and technical articles!
