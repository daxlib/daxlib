# Tharun.TimeConversion - Usage Guide

This package provides DAX UDF functions to convert total seconds into human-readable time formats (minutes, hours, and days).

## Functions Overview

### 1. Tharun.TimeConversion.ConvertSecondsToMinutes
Converts total seconds into Minutes:Seconds (MM:SS) format.

**Parameters:**
- `Seconds` (int64): Total number of seconds to convert
- `WithSymbols` (boolean): If TRUE, returns format like "2m:5s"; if FALSE, returns "02:05"

**Usage Examples:**

| Input | WithSymbols | Output |
|-------|------------|--------|
| 65 | TRUE | "1m:5s" |
| 65 | FALSE | "01:05" |
| 125 | TRUE | "2m:5s" |
| 125 | FALSE | "02:05" |
| 5 | TRUE | "0m:5s" |
| 5 | FALSE | "00:05" |
| 3599 | TRUE | "59m:59s" |
| 3599 | FALSE | "59:59" |

**DAX Example:**
```dax
// Display with symbols
Tharun.TimeConversion.ConvertSecondsToMinutes(125, TRUE())  // Returns "2m:5s"

// Display in standard time format
Tharun.TimeConversion.ConvertSecondsToMinutes(125, FALSE()) // Returns "02:05"
```

---

### 2. Tharun.TimeConversion.ConvertSecondsToHours
Converts total seconds into Hours:Minutes:Seconds (HH:MM:SS) format.

**Parameters:**
- `Seconds` (int64): Total number of seconds to convert
- `WithSymbols` (boolean): If TRUE, returns format like "1h:2m:5s"; if FALSE, returns "01:02:05"

**Usage Examples:**

| Input | WithSymbols | Output |
|-------|------------|--------|
| 3600 | TRUE | "1h:0m:0s" |
| 3600 | FALSE | "01:00:00" |
| 3725 | TRUE | "1h:2m:5s" |
| 3725 | FALSE | "01:02:05" |
| 125 | TRUE | "0h:2m:5s" |
| 125 | FALSE | "00:02:05" |
| 86399 | TRUE | "23h:59m:59s" |
| 86399 | FALSE | "23:59:59" |

**DAX Example:**
```dax
// Display with symbols (useful for human-readable reports)
Tharun.TimeConversion.ConvertSecondsToHours(3725, TRUE())  // Returns "1h:2m:5s"

// Display in compact time format (useful for data imports/exports)
Tharun.TimeConversion.ConvertSecondsToHours(3725, FALSE()) // Returns "01:02:05"
```

---

### 3. Tharun.TimeConversion.ConvertSecondsToDays
Converts total seconds into Days:Hours:Minutes:Seconds (DD:HH:MM:SS) format.

**Parameters:**
- `Seconds` (int64): Total number of seconds to convert
- `WithSymbols` (boolean): If TRUE, returns format like "1d:1h:1m:1s"; if FALSE, returns "01:01:01:01"

**Usage Examples:**

| Input | WithSymbols | Output |
|-------|------------|--------|
| 86400 | TRUE | "1d:0h:0m:0s" |
| 86400 | FALSE | "01:00:00:00" |
| 90061 | TRUE | "1d:1h:1m:1s" |
| 90061 | FALSE | "01:01:01:01" |
| 3725 | TRUE | "0d:1h:2m:5s" |
| 3725 | FALSE | "00:01:02:05" |
| 864000 | TRUE | "10d:0h:0m:0s" |
| 864000 | FALSE | "10:00:00:00" |

**DAX Example:**
```dax
// Display with symbols (great for dashboards and reports)
Tharun.TimeConversion.ConvertSecondsToDays(90061, TRUE())  // Returns "1d:1h:1m:1s"

// Display in standard format (great for data tables)
Tharun.TimeConversion.ConvertSecondsToDays(90061, FALSE()) // Returns "01:01:01:01"
```

---

## Real-World Scenarios

### Scenario 1: Video Duration Display
Convert video length stored in seconds to human-readable format.

```dax
// Video is 7325 seconds long
Tharun.TimeConversion.ConvertSecondsToHours(7325, TRUE())  // Returns "2h:2m:5s"
```

### Scenario 2: Process Execution Time
Display how long a data process took to complete.

```dax
// Process took 4850 seconds
Tharun.TimeConversion.ConvertSecondsToMinutes(4850, FALSE()) // Returns "80:50" (80 minutes and 50 seconds)
```

### Scenario 3: Project Duration Tracking
Show project timeline in days, hours, minutes format.

```dax
// Project took 259235 seconds
Tharun.TimeConversion.ConvertSecondsToDays(259235, TRUE())  // Returns "3d:0h:1m:35s"
```

### Scenario 4: Activity Log Report
Create a report column showing how long activities took.

```dax
// Multiple activities in a table
Activity 1: Tharun.TimeConversion.ConvertSecondsToHours(1800, TRUE())   // Returns "0h:30m:0s"
Activity 2: Tharun.TimeConversion.ConvertSecondsToHours(5400, TRUE())   // Returns "1h:30m:0s"
Activity 3: Tharun.TimeConversion.ConvertSecondsToHours(36000, TRUE())  // Returns "10h:0m:0s"
```

---

## Notes

- All three functions use the same `WithSymbols` parameter logic
- Use `WithSymbols = TRUE()` for user-facing reports and dashboards
- Use `WithSymbols = FALSE()` for data export, import, or system integrations
- Functions automatically handle leading zeros in the non-symbol format
- The functions are composable - `Tharun.TimeConversion.ConvertSecondsToHours` uses `Tharun.TimeConversion.ConvertSecondsToMinutes` internally for calculation

---

## About the Author

For more insights on DAX, Power BI, and data analytics, checkout my website: **[techietips.co.in](https://techietips.co.in)** to read all my blogs and technical articles!
