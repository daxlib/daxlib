# PQL.Assert - DAX Unit Testing Library

A comprehensive DAX assertion library for writing unit tests in Power BI and Analysis Services semantic models. PQL.Assert provides standardized assertion functions to validate data, calculations, and model integrity.

## 🚀 Getting Started

### Installation

1. Load the `functions.tmdl` file into your semantic model
2. Refresh the model to make functions available
3. Start writing tests using PQL.Assert functions

### Basic Usage

```dax
// Basic assertion example
EVALUATE PQL.Assert.ShouldEqual("Test 1: 2+2 should equal 4", 4, 2+2)
```

## 📚 Function Reference

**NULL vs blank.** Throughout the library, **NULL** means DAX `BLANK()` (a missing value) and **blank** means an empty string `""`. In DAX, `BLANK() = 0`, `BLANK() = ""` and `BLANK() = FALSE` are all TRUE and `COUNTBLANK` counts both NULL and `""` ([SQLBI: Handling BLANK in DAX](https://www.sqlbi.com/articles/blank-handling-in-dax/)), so PQL.Assert applies these rules explicitly:

- Functions named `…Null…` check `BLANK()` only, `…Blank…` checks `""` only, and `…NullOrBlank…` checks either. `ShouldBeBlank(BLANK())` fails; `ShouldBeNullOrBlank(BLANK())` passes.
- NULL is never treated as 0 or `""`. Numeric, text, set and uniqueness checks skip NULL values; statistics (mean, percentile, standard deviation) are computed over rows with NULL skipped.
- A check on a column's values fails with **NO VALUES** when the column has no non-NULL value, rather than passing or failing depending on whether 0 is in range. Row-based rates fail with **NO ROWS** on an empty table. Checks that count problems (`NullCountShouldBeAtMost`, `Tbl.RowCountShouldBeAtMost`) pass at zero.
- A NULL `actual` value fails the scalar numeric and string checks (`ShouldBeGreaterThan`, `ShouldBeBetween`, `ShouldStartWith`, …), and `ShouldEqual` / `ShouldEqualExactly` treat NULL and `""` as different. Exception: `ShouldBeTrue` / `ShouldBeFalse` take a BOOLEAN parameter, and DAX converts `BLANK()` to FALSE before the function runs, so `ShouldBeFalse(BLANK())` passes. Check with `ShouldNotBeNull` first when a condition can be BLANK.
- Deprecated 0.7.x functions keep their old behavior; see [Deprecated functions and replacements](#deprecated-functions-and-replacements). Functions changed in place are listed in [Behavior changes in 0.8.0](#behavior-changes-in-080).

### Library Version

- `PQL.Assert.LibraryVersion()` - Returns the installed version as text, e.g. `"0.8.0"`. Added in 0.8.0, so if `EVALUATE { PQL.Assert.LibraryVersion() }` errors, the model has 0.7.x or older

### Basic Assertions

- `PQL.Assert.ShouldBeTrue(testName, actualCondition)` - Asserts condition is TRUE
- `PQL.Assert.ShouldBeFalse(testName, actualCondition)` - Asserts condition is FALSE
- `PQL.Assert.ShouldBeNull(testName, actualValue)` - Asserts value is NULL (BLANK())
- `PQL.Assert.ShouldNotBeNull(testName, actualValue)` - Asserts value is not NULL
- `PQL.Assert.ShouldBeBlank(testName, actualValue)` - Asserts value is empty string ("")
- `PQL.Assert.ShouldNotBeBlank(testName, actualValue)` - Asserts value is not empty string
- `PQL.Assert.ShouldBeNullOrBlank(testName, actualValue)` - Asserts value is NULL or empty string
- `PQL.Assert.ShouldNotBeNullOrBlank(testName, actualValue)` - Asserts value has content

### Equality Assertions

- `PQL.Assert.ShouldEqual(testName, expected, actual)` - Asserts values are equal
- `PQL.Assert.ShouldNotEqual(testName, notExpected, actual)` - Asserts values are not equal
- `PQL.Assert.ShouldEqualExactly(testName, expected, actual)` - Case-sensitive string equality

### Numeric Comparisons

- `PQL.Assert.ShouldBeGreaterThan(testName, threshold, actual)` - Asserts actual > threshold
- `PQL.Assert.ShouldBeLessThan(testName, threshold, actual)` - Asserts actual < threshold
- `PQL.Assert.ShouldBeGreaterOrEqual(testName, threshold, actual)` - Asserts actual >= threshold
- `PQL.Assert.ShouldBeLessOrEqual(testName, threshold, actual)` - Asserts actual <= threshold
- `PQL.Assert.ShouldBeBetween(testName, lowerBound, upperBound, actual)` - Asserts value within range
- `PQL.Assert.ShouldNotBeBetween(testName, lowerBound, upperBound, actual)` - Asserts value is outside the inclusive range (actual < lowerBound or actual > upperBound)

### String Assertions

- `PQL.Assert.ShouldStartWith(testName, prefix, actual)` - Asserts string starts with prefix
- `PQL.Assert.ShouldEndWith(testName, suffix, actual)` - Asserts string ends with suffix
- `PQL.Assert.ShouldContainString(testName, substring, actual)` - Asserts string contains substring
- `PQL.Assert.ShouldMatch(testName, pattern, actual)` - Asserts string contains the pattern. Uses `CONTAINSSTRING`: case-insensitive, substring, `?` and `*` wildcards. It is not a regular expression and does not anchor to the whole value

### Column Assertions

#### Null & Blank Checks

| Check | NULL only | Blank (`""`) only | NULL or blank |
|---|---|---|---|
| Every row is… | `Col.ShouldBeNull` | — | `Col.ShouldBeNullOrBlank` |
| No row is… | `Col.NullCountShouldBeAtMost(…, 0)` | `Col.BlankRateShouldBeAtMostV2(…, 0)` | `Col.ShouldNotBeNullOrBlank` |
| At most N% of rows are… | `Col.NullRateShouldBeAtMostV2` | `Col.BlankRateShouldBeAtMostV2` | `Col.NullOrBlankRateShouldBeAtMostV2` |

- `PQL.Assert.Col.ShouldBeNull(testName, columnRef)` - Asserts every value is NULL
- `PQL.Assert.Col.ShouldNotBeNull(testName, columnRef)` - Asserts the column has at least one non-NULL value. It does not check that no row is NULL; use `Col.NullCountShouldBeAtMost(testName, tableRef, columnRef, 0)` for that
- `PQL.Assert.Col.ShouldBeNullOrBlank(testName, columnRef)` - Asserts every value is NULL or an empty string
- `PQL.Assert.Col.ShouldNotBeNullOrBlank(testName, columnRef)` - Asserts no value is NULL or an empty string
- `PQL.Assert.Col.ShouldBeBlank` and `PQL.Assert.Col.ShouldNotBeBlank` are deprecated. Despite their names they are duplicates of the `NullOrBlank` versions; see [Deprecated functions and replacements](#deprecated-functions-and-replacements)

#### Presence & Completeness Thresholds

These take the table as well as the column, because a column reference alone gives distinct values, not rows. Percentages are 0-100. An empty table fails with NO ROWS. `tableRef` can be any table expression (a model table, a `FILTER(...)` or a `DATATABLE`), and `columnRef` must be a column of that table; no column names are assumed.

- `PQL.Assert.Col.NullCountShouldBeAtMost(testName, tableRef, columnRef, maxCount)` - Asserts at most `maxCount` rows are NULL. Pass `0` to require that no row is NULL. Empty strings are not counted
- `PQL.Assert.Col.NullRateShouldBeAtMostV2(testName, tableRef, columnRef, maxNullPct)` - Asserts the share of NULL rows is at most the percentage
- `PQL.Assert.Col.BlankRateShouldBeAtMostV2(testName, tableRef, columnRef, maxBlankPct)` - Asserts the share of empty-string rows is at most the percentage. Works on any data type
- `PQL.Assert.Col.NullOrBlankRateShouldBeAtMostV2(testName, tableRef, columnRef, maxPct)` - Asserts the share of NULL or empty-string rows is at most the percentage
- `PQL.Assert.Col.PopulatedRateShouldBeAtLeastV2(testName, tableRef, columnRef, minPct)` - Asserts the share of rows that are neither NULL nor empty is at least the percentage

Example: `PQL.Assert.Col.NullRateShouldBeAtMostV2("Year mostly filled", 'Movies', 'Movies'[Year], 5)`.

The 0.7.x rate functions without `V2` are deprecated. See [Deprecated functions and replacements](#deprecated-functions-and-replacements).

#### Uniqueness & Key-Like Behavior

- `PQL.Assert.Col.NonNullValuesShouldBeDistinct(testName, columnRef)` - Asserts non-NULL values are all distinct. NULL rows are ignored; empty strings count as values
- `PQL.Assert.Col.ShouldBePrimaryKey(testName, columnRef)` - Asserts no NULL or empty-string rows and all values distinct
- `PQL.Assert.Col.DuplicateCountShouldBeAtMost(testName, columnRef, maxCount)` - Asserts at most `maxCount` duplicates among non-NULL values (non-NULL rows minus distinct values)
- `PQL.Assert.Col.ShouldBeUniqueWithin(testName, columnRef, partitionByCol)` - Asserts column values are unique within partitions defined by another column
- `PQL.Assert.Col.ComboShouldBeDistinct(testName, columnRef1, columnRef2)` - Asserts the combination of two columns forms a distinct composite key. For three or more columns, use `Tbl.RowsShouldBeDistinct`

`Col.ShouldBeDistinct` and `Col.ShouldNotContainDuplicates` are deprecated. See [Deprecated functions and replacements](#deprecated-functions-and-replacements).

#### Deprecated functions and replacements

Deprecated functions still work and give the same results as in 0.7.x, so existing suites don't change. They will not be removed before 1.0. Use the replacements in new tests.

| Deprecated (0.7.x) | Replacement (0.8.0) | What changes when you switch |
|---|---|---|
| `Col.NullRateShouldBeAtMost(testName, columnRef, maxNullPct)` | `Col.NullRateShouldBeAtMostV2(testName, tableRef, columnRef, maxNullPct)` | Divides by rows, not distinct values. Counts only NULL; the old function also counted `""` as NULL. To keep counting both, use `NullOrBlankRateShouldBeAtMostV2` |
| `Col.BlankRateShouldBeAtMost(testName, columnRef, maxBlankPct)` | `Col.BlankRateShouldBeAtMostV2(testName, tableRef, columnRef, maxBlankPct)` | Counts every `""` row; the old function counted at most one, because it looked at distinct values. Divides by rows |
| `Col.NullOrBlankRateShouldBeAtMost(testName, columnRef, maxPct)` | `Col.NullOrBlankRateShouldBeAtMostV2(testName, tableRef, columnRef, maxPct)` | Counts every NULL and `""` row, not at most one of each. Divides by rows |
| `Col.PopulatedRateShouldBeAtLeast(testName, columnRef, minPct)` | `Col.PopulatedRateShouldBeAtLeastV2(testName, tableRef, columnRef, minPct)` | Counts populated rows, not populated distinct values. Divides by rows |
| `Col.ShouldBeBlank(testName, columnRef)` | `Col.ShouldBeNullOrBlank(testName, columnRef)` | Nothing. Same logic and results; only the name changes |
| `Col.ShouldNotBeBlank(testName, columnRef)` | `Col.ShouldNotBeNullOrBlank(testName, columnRef)`, or `Col.BlankRateShouldBeAtMostV2(testName, tableRef, columnRef, 0)` to check for empty strings only | `ShouldNotBeNullOrBlank`: nothing, same logic and results. `BlankRateShouldBeAtMostV2(…, 0)`: NULL rows are allowed |
| `Col.ShareOfZerosAtMost(testName, columnRef, maxPct)` | `Col.ShareOfZerosAtMostV2(testName, tableRef, columnRef, maxPct)` | NULL is no longer counted as a zero. Divides by non-NULL rows, not distinct values |
| `Col.ShareOfEvensAtLeast(testName, columnRef, minPct)` | `Col.ShareOfEvensAtLeastV2(testName, tableRef, columnRef, minPct)` | NULL is no longer counted as even. Divides by non-NULL rows, not distinct values |
| `Col.ShareOfOddsAtLeast(testName, columnRef, minPct)` | `Col.ShareOfOddsAtLeastV2(testName, tableRef, columnRef, minPct)` | NULL is no longer in the denominator. Divides by non-NULL rows, not distinct values |
| `Col.ShouldBeDistinct(testName, columnRef)` | `Col.NonNullValuesShouldBeDistinct(testName, columnRef)`, or `Col.ShouldBePrimaryKey(testName, columnRef)` when NULLs are not allowed | Handles NULL correctly. See below |
| `Col.ShouldNotContainDuplicates(testName, columnRef)` | `Col.NonNullValuesShouldBeDistinct(testName, columnRef)`, or `Col.DuplicateCountShouldBeAtMost(testName, columnRef, 0)` | Same as above |

Migration is mechanical: add the table as the second argument and keep the rest. For example, `Col.NullRateShouldBeAtMost("Year filled", 'Movies'[Year], 5)` becomes `Col.NullRateShouldBeAtMostV2("Year filled", 'Movies', 'Movies'[Year], 5)`. The percentage is still 0-100.

**Why the rate replacements are better.** A column reference on its own gives the column's distinct values, not its rows. The 0.7.x rate functions divide by `COUNTROWS(ALLNOBLANKROW(column))`, which is the number of distinct values. They also count the numerator over distinct values, except the null rate, which counts rows with `COUNTBLANK`. The result is a rate that is wrong whenever values repeat, which is nearly always. A column with 815 NULLs in 16,376 rows (4.98%) and 76 distinct values reports "1072.37% (815 of 76)", so every threshold except 0% is meaningless. The V2 functions take the table as well, so they divide by `COUNTROWS(table)` and count matching rows. They also:

- treat NULL and `""` consistently with the rest of the library: NULL is `BLANK()`, blank is `""`;
- report the row count, e.g. `"4.98% (815 of 16376 rows)"`, and format percentages without a trailing dot (`"5%"`, not `"5.%"`);
- return `0` instead of an empty value when nothing matches. The old null rate on a column with no NULLs shows `"% ( of 3)"`.

On the TestingModel fixture `TestData[Short Text]` (`"x"`, `""`, `"Error - Timeout"`, `"Err"`, NULL), the old null rate reports 40% because it counts the empty string. `NullRateShouldBeAtMostV2` reports 20% (1 of 5 rows). On `TestData[Dup With Blank]` (`1, 1, 2, 3`, NULL), the old null rate reports 25% (1 of 4 distinct values) and V2 reports 20% (1 of 5 rows).

**Why `Col.ShouldBeBlank` and `Col.ShouldNotBeBlank` are deprecated.** The scalar `PQL.Assert.ShouldBeBlank` checks for `""` only, but 0.7.x `Col.ShouldBeBlank` and `Col.ShouldNotBeBlank` have the same logic as `Col.ShouldBeNullOrBlank` and `Col.ShouldNotBeNullOrBlank`. So `Col.ShouldBeBlank` passes an all-NULL column, and `Col.ShouldNotBeBlank` fails a column that has NULLs but no empty strings (`TestData[NullableField]` reports "HAS BLANK VALUES (2 blank)"). Their results are unchanged; switching to the `NullOrBlank` name makes the test say what it checks.

**Why the share replacements are better.** In DAX, `BLANK() = 0` is TRUE and `MOD(BLANK(), 2)` is 0 ([SQLBI: Handling BLANK in DAX](https://www.sqlbi.com/articles/blank-handling-in-dax/)). The 0.7.x share checks compare each value with `=` without testing `ISBLANK` first, so NULL counts as a zero and as even. They also divide by distinct values. On `TestData[Dup With Blank]` (`1, 1, 2, 3`, NULL), which has no zeros, `Col.ShareOfZerosAtMost(…, 0)` reports 25% and fails. The V2 functions skip NULL rows, which matches `ShouldBeNonNegative`, `MinShouldBeAtLeast` and the other numeric checks, and they count rows.

**Why the uniqueness replacements are better.** `Col.ShouldBeDistinct` compares `DISTINCTCOUNT`, which counts NULL as a value, with `COUNTA`, which ignores NULL. So:

- a column that is distinct apart from one NULL fails (`{10, 20, 30, 40, NULL}`: 5 distinct vs 4 values);
- a NULL can hide a duplicate, so the column passes (`{1, 1, 2, 3, NULL}`: 4 distinct vs 4 values).

`Col.NonNullValuesShouldBeDistinct` compares `DISTINCTCOUNTNOBLANK` with `COUNTA`, so NULLs are ignored on both sides and duplicates are always caught. `Col.ShouldBePrimaryKey` also fails on any NULL or `""` row. `Col.DuplicateCountShouldBeAtMost` reports how many duplicates there are and lets you allow a few.

#### Behavior changes in 0.8.0

These functions keep their names and parameters but follow the NULL rules above. Results change only on NULL input, an all-NULL column or an empty table. Mean, percentile and standard deviation also change on columns with repeated values, because 0.7.x computed them over distinct values.

| Function | 0.7.x | 0.8.0 |
|---|---|---|
| `Col.ShouldBePositive`, `Col.ShouldBeNonZero` | Counted NULL as non-positive / zero (`BLANK() <= 0` and `BLANK() = 0` are TRUE): `{10, 20, 30, 40, NULL}` failed | NULL skipped; that column passes |
| `Col.MeanShouldBeInRange` | Averaged distinct values: `{1, 1, 2, 3, NULL}` gave 2 | Averages rows, NULL skipped: 1.75 |
| `Col.PctileShouldBeInRange` | Distinct values, NULL ranked lowest: P50 of `{10, 20, 30, 40, NULL}` was 20, P0 was blank | Rows, NULL skipped: P50 25, P0 10 |
| `Col.StdDevShouldBeAtMost` | Distinct values, NULL counted as 0: 16.77 on `{10, 20, 30, 40, NULL}` | Sample standard deviation over rows, NULL skipped: 12.91 |
| `Col.ShouldBeInRange`, `Col.MinShouldBeAtLeast`, `Col.MaxShouldBeAtMost`, `Col.MeanShouldBeInRange`, `Col.PctileShouldBeInRange`, `Col.StdDevShouldBeAtMost` on an all-NULL column | Passed or failed depending on whether 0 was in range (`ShouldBeInRange(-5, 5)` passed with "Min=, Max=") | Fails with NO VALUES |
| `Col.ShouldBeNonNegative`, `Col.ShouldBePositive`, `Col.ShouldBeNonZero`, the text checks (`ShouldStartWith`, `ShouldEndWith`, `ShouldContainString` and their `Not` forms, `TextShouldBeTrimmed`, `TextShouldHaveNoLeadingOrTrailingSpaces`, `TextCaseShouldBeUpper`/`Lower`, `TextLengthShouldBeInRange`), `Col.ShouldBeUniqueWithin`, `Col.ComboShouldBeDistinct` on an all-NULL column | Passed (nothing to check) | Fails with NO VALUES |
| `Col.ValuesShouldBeInSet`, `Col.ValuesShouldNotBeInSet` | Treated NULL as a value: a NULL was "outside" the allowed set | NULL skipped; an all-NULL column fails with NO VALUES |
| `ShouldBeGreaterThan`, `ShouldBeLessThan`, `ShouldBeGreaterOrEqual`, `ShouldBeLessOrEqual`, `ShouldBeBetween` | Compared a NULL `actual` as 0: `ShouldBeGreaterThan(-1, BLANK())` passed | A NULL `actual` fails, shown as "NULL" |
| `ShouldStartWith`, `ShouldEndWith`, `ShouldContainString`, `ShouldMatch` | Compared a NULL `actual` as `""`: `ShouldStartWith("", BLANK())` passed | A NULL `actual` fails |
| `ShouldEqualExactly` | `EXACT` treated NULL as `""`: `ShouldEqualExactly("", BLANK())` passed | NULL equals only NULL, like `ShouldEqual` |

#### Range & Distribution Checks

- `PQL.Assert.Col.ShouldBeInRange(testName, columnRef, minIncl, maxIncl)` - Asserts all numeric column values fall within the specified inclusive range
- `PQL.Assert.Col.MinShouldBeAtLeast(testName, columnRef, minIncl)` - Asserts minimum value in column is at least the specified value
- `PQL.Assert.Col.MaxShouldBeAtMost(testName, columnRef, maxIncl)` - Asserts maximum value in column is at most the specified value
- `PQL.Assert.Col.MeanShouldBeInRange(testName, columnRef, minIncl, maxIncl)` - Asserts mean of column falls within the specified range
- `PQL.Assert.Col.PctileShouldBeInRange(testName, columnRef, pct, minIncl, maxIncl)` - Asserts specified percentile of column falls within the given range
- `PQL.Assert.Col.StdDevShouldBeAtMost(testName, columnRef, maxStdDev)` - Asserts standard deviation of column is at most the specified value
- `PQL.Assert.Col.ShareOfZerosAtMostV2(testName, tableRef, columnRef, maxPct)` - Asserts the share of zero values among non-NULL rows is at most the percentage
- `PQL.Assert.Col.ShareOfEvensAtLeastV2(testName, tableRef, columnRef, minPct)` - Asserts the share of even values among non-NULL rows is at least the percentage
- `PQL.Assert.Col.ShareOfOddsAtLeastV2(testName, tableRef, columnRef, minPct)` - Asserts the share of odd values among non-NULL rows is at least the percentage
- `Col.ShareOfZerosAtMost`, `Col.ShareOfEvensAtLeast` and `Col.ShareOfOddsAtLeast` (no `V2`) are deprecated; see [Deprecated functions and replacements](#deprecated-functions-and-replacements)

Numeric checks skip NULL rows: NULL is a missing value, not 0. Mean, percentile and standard deviation are computed over rows. A column with no non-NULL value fails with NO VALUES.

#### Sign & Monotonicity

- `PQL.Assert.Col.ShouldBeNonNegative(testName, columnRef)` - Asserts all values in column are non-negative (>= 0)
- `PQL.Assert.Col.ShouldBePositive(testName, columnRef)` - Asserts all non-NULL values are strictly positive (> 0). NULL is skipped (changed in 0.8.0; see [Behavior changes in 0.8.0](#behavior-changes-in-080))
- `PQL.Assert.Col.ShouldBeNonZero(testName, columnRef)` - Asserts no non-NULL value is zero. NULL is skipped (changed in 0.8.0)
- `PQL.Assert.Col.ShouldBeMonotonicIncreasing(testName, columnRef, orderedByCol)` - Asserts values monotonically strictly increase when ordered by another column
- `PQL.Assert.Col.ShouldBeMonotonicNonDecreasing(testName, columnRef, orderedByCol)` - Asserts values monotonically non-decrease when ordered by another column
- `PQL.Assert.Col.ShouldBeMonotonicDecreasing(testName, columnRef, orderedByCol)` - Asserts values monotonically strictly decrease when ordered by another column

#### Categorical, Membership & Format

- `PQL.Assert.Col.ValuesShouldBeInSet(testName, columnRef, allowedValuesTable, allowedValuesColumn)` - Asserts all column values are in the specified allowed set (model-independent: explicitly specify table and column)
- `PQL.Assert.Col.ValuesShouldNotBeInSet(testName, columnRef, bannedValuesTable, bannedValuesColumn)` - Asserts no column values are in the specified banned set (model-independent: explicitly specify table and column)
- `PQL.Assert.Col.DistinctCountShouldBeAtMost(testName, columnRef, maxDistinct)` - Asserts distinct count of values is at most the specified number. NULL counts as one value (DAX `DISTINCTCOUNT`)
- `PQL.Assert.Col.DistinctCountShouldBeAtLeast(testName, columnRef, minDistinct)` - Asserts distinct count of values is at least the specified number. NULL counts as one value (DAX `DISTINCTCOUNT`)
- `PQL.Assert.Col.TextShouldHaveNoLeadingOrTrailingSpaces(testName, columnRef)` - Asserts no text values have leading or trailing spaces
- `PQL.Assert.Col.TextShouldBeTrimmed(testName, columnRef)` - Asserts all text values are trimmed (alias for TextShouldHaveNoLeadingOrTrailingSpaces)
- `PQL.Assert.Col.TextCaseShouldBeUpper(testName, columnRef)` - Asserts all text values are uppercase
- `PQL.Assert.Col.TextCaseShouldBeLower(testName, columnRef)` - Asserts all text values are lowercase
- `PQL.Assert.Col.TextLengthShouldBeInRange(testName, columnRef, minLen, maxLen)` - Asserts text lengths fall within the specified range

#### Text Prefix, Suffix & Containment

DAX has no `STARTSWITH`/`ENDSWITH` functions, so these assertions use `LEFT()` and `RIGHT()`. Each accepts an optional `matchCase` argument — pass `"Y"` (or `"YES"`/`"TRUE"`/`"1"`) for case-sensitive comparison. When omitted, comparison is case-insensitive. NULL values are skipped; empty strings are checked like any other value.

- `PQL.Assert.Col.ShouldStartWith(testName, columnRef, prefix, [matchCase])` - Asserts every non-blank value starts with the prefix
- `PQL.Assert.Col.ShouldNotStartWith(testName, columnRef, prefix, [matchCase])` - Asserts no non-blank value starts with the prefix
- `PQL.Assert.Col.ShouldEndWith(testName, columnRef, suffix, [matchCase])` - Asserts every non-blank value ends with the suffix
- `PQL.Assert.Col.ShouldNotEndWith(testName, columnRef, suffix, [matchCase])` - Asserts no non-blank value ends with the suffix
- `PQL.Assert.Col.ShouldContainString(testName, columnRef, substring, [matchCase])` - Asserts every non-blank value contains the substring
- `PQL.Assert.Col.ShouldNotContainString(testName, columnRef, substring, [matchCase])` - Asserts no non-blank value contains the substring

#### Freshness

- `PQL.Assert.Col.MaxDateShouldBeWithinDays(testName, columnRef, maxAgeDays)` - Asserts the latest date is at most `maxAgeDays` days before `UTCNOW()`. Fails when the column has no values. A column stored in local time is off by the UTC offset
- `PQL.Assert.Col.MaxDateShouldBeWithinHours(testName, columnRef, maxAgeHours)` - Asserts the latest datetime is at most `maxAgeHours` hours before `UTCNOW()`, e.g. a refresh-log timestamp that must be no older than 3 hours: `PQL.Assert.Col.MaxDateShouldBeWithinHours("Refreshed in last 3h", 'Refresh Log'[Refreshed At UTC], 3)`. Fails when the column has no values. Store the column in UTC

#### Schema & Existence

- `PQL.Assert.Col.ShouldExist(testName, tableName, columnName)` - Asserts column exists
- `PQL.Assert.Col.IsAvailableInMDX(testName, tableName, columnName)` - Asserts column is available to MDX clients such as Analyze in Excel. The assertion resolves the column ID from `INFO.VIEW.COLUMNS()` and reads `IsAvailableInMDX` from `INFO.COLUMNS()`.

### Table Assertions

#### Row Checks

- `PQL.Assert.Tbl.ShouldHaveRows(testName, tableRef)` - Asserts table has at least one row
- `PQL.Assert.Tbl.ShouldHaveRowCount(testName, tableRef, expectedRowCount)` - Asserts exact row count
- `PQL.Assert.Tbl.ShouldHaveMoreRowsThan(testName, threshold, tableToCheck)` - Asserts row count > threshold
- `PQL.Assert.Tbl.RowCountShouldBeAtMost(testName, filteredTable, maxCount)` - Asserts a filtered table has at most `maxCount` rows, e.g. `FILTER('Orders', NOT 'Orders'[Status] IN {"open", "closed"})`
- `PQL.Assert.Tbl.RowRateShouldBeAtMost(testName, filteredTable, baseTable, maxPct)` - Asserts a filtered table's rows are at most `maxPct` percent (0-100) of the base table's rows. An empty base table fails with NO ROWS
- `PQL.Assert.Tbl.RowsShouldBeDistinct(testName, keyTable)` - Asserts every row of a key projection is distinct, for composite keys of any width, e.g. `SELECTCOLUMNS('Sales', "OrderID", 'Sales'[OrderID], "Line", 'Sales'[Line])`. An empty key table fails with NO ROWS

#### Schema & Column Shape

- `PQL.Assert.Tbl.ShouldExist(testName, tableName)` - Asserts table exists
- `PQL.Assert.Tbl.ShouldHaveColumns(testName, tableName, columnNamesList)` - Asserts table has all specified columns (comma-separated list)
- `PQL.Assert.Tbl.ShouldNotHaveExtraColumns(testName, tableName, allowedColumnNamesList)` - Asserts table does not have extra columns beyond the allowed list (comma-separated)
- `PQL.Assert.Tbl.ColumnDataTypeShouldBe(testName, tableName, columnName, expectedType)` - Asserts column has the expected data type

These three functions take `tableName` as a text string, not a table reference. For example: `PQL.Assert.Tbl.ShouldHaveColumns("Sales has key columns", "Sales", "OrderID,Amount")`.

### Relationship Assertions

- `PQL.Assert.Relationship.ShouldExist(testName, fromTable, fromColumn, toTable, toColumn, [fromCardinality], [toCardinality], [securityFilteringBehavior])` - Asserts a relationship exists between two columns, with optional cardinality and security filtering checks. Pass `""` (or omit) any optional argument to skip that check.
  - `fromCardinality` (optional) — expected From-side cardinality: `"One"` or `"Many"`.
  - `toCardinality` (optional) — expected To-side cardinality: `"One"` or `"Many"`.
  - `securityFilteringBehavior` (optional) — expected filter direction: `"OneDirection"` or `"BothDirections"`.

**Example Usage:**
```dax
// Existence only
EVALUATE PQL.Assert.Relationship.ShouldExist(
    "Sales-Customer relationship exists",
    "Sales", "CustomerID", "Customers", "CustomerID"
)

// Full validation of cardinality and security filtering
EVALUATE PQL.Assert.Relationship.ShouldExist(
    "Sales-Customer is Many-to-One, single direction",
    "Sales", "CustomerID", "Customers", "CustomerID",
    "Many", "One", "OneDirection"
)
```

### Partition Assertions

PQL.Assert provides functions for validating partition configurations in semantic models, useful for incremental refresh scenarios and data quality monitoring.

- `PQL.Assert.Partitions.ShouldBe(testName, tableName, expectedCount, [startDate], [endDate])` - Asserts that a table has the expected number of partitions. Optional date parameters filter partitions by `RangeStart` and `RangeEnd` for targeted testing of time-based partition strategies.
- `PQL.Assert.Partitions.ShouldExist(testName, tableName, [startDate], [endDate])` - Asserts that at least one partition exists for a table (or within the filtered date window).
- `PQL.Assert.Partitions.ShouldBeAtLeast(testName, tableName, minimumCount, [startDate], [endDate])` - Asserts that a table has at least a minimum number of partitions. Useful for validating incremental refresh partition growth.

**Example Usage:**
```dax
// Basic partition count validation
EVALUATE PQL.Assert.Partitions.ShouldBe(
    "Sales table should have 12 monthly partitions",
    "Sales",
    12
)

// Partition count with date range filtering
EVALUATE PQL.Assert.Partitions.ShouldBe(
    "Sales partitions for 2024 should be complete",
    "Sales",
    12,
    DATE(2024, 1, 1),
    DATE(2024, 12, 31)
)

// Partition count should be at least a minimum threshold
EVALUATE PQL.Assert.Partitions.ShouldBeAtLeast(
    "Sales should have at least 10 partitions",
    "Sales",
    10
)

// Partition existence validation
EVALUATE PQL.Assert.Partitions.ShouldExist(
    "Sales should have at least one partition",
    "Sales"
)
```

### Perspective Assertions

PQL.Assert provides functions for validating perspective configurations and membership, ensuring that perspectives contain the expected tables, columns, and measures.

- `PQL.Assert.Perspective.ShouldExist(testName, perspectiveName)` - Asserts that a perspective exists in the semantic model
- `PQL.Assert.Perspective.ShouldContain(testName, perspectiveName, expectedTablesList, expectedColumnsList, expectedMeasuresList)` - Validates a perspective contains expected objects using comma-separated lists. Checks if all expected tables, columns, and measures are present in the perspective (does not fail if extra objects exist). Uses delimiter-safe case-insensitive matching to avoid substring false positives.
- `PQL.Assert.Perspective.ShouldMatchSchema(testName, perspectiveName, expectedTablesList, expectedColumnsList, expectedMeasuresList)` - Exact perspective schema validation using comma-separated lists (consistent with other schema checks like `Tbl.ShouldHaveColumns` and `Tbl.ShouldNotHaveExtraColumns`). This validates both missing expected objects and unexpected extra objects.

**Example Usage:**
```dax
// Check if perspective exists
EVALUATE PQL.Assert.Perspective.ShouldExist(
    "Finance perspective should exist",
    "Finance"
)

// Validate perspective contains expected tables
EVALUATE PQL.Assert.Perspective.ShouldContain(
    "Finance perspective should contain core tables",
    "Finance",
    "Sales,Customers,Products",  // Expected tables
    "",                           // No expected columns
    ""                            // No expected measures
)

// Validate with columns and measures
EVALUATE PQL.Assert.Perspective.ShouldContain(
    "Finance perspective should contain specific columns and measures",
    "Finance",
    "",                                           // No expected tables
    "Sales[Amount],Sales[OrderDate]",            // Expected columns
    "Sales[Total Sales],Sales[YTD Sales]"        // Expected measures
)

// Validate with all object types
EVALUATE PQL.Assert.Perspective.ShouldContain(
    "Finance perspective should contain core objects",
    "Finance",
    "Sales,Customers",
    "Sales[Amount],Customers[CustomerName]",
    "Sales[Total Sales]"
)

// Exact schema validation - ensures no extra objects exist
EVALUATE PQL.Assert.Perspective.ShouldMatchSchema(
    "Finance perspective should match expected schema",
    "Finance",
    "Sales,Customers,Products",
    "Sales[Amount],Sales[OrderDate],Customers[CustomerName]",
    "Sales[Total Sales],Sales[YTD Sales]"
)
```

### Object Level Security (OLS) Assertions

PQL.Assert provides dedicated functions for testing Object Level Security at both the table and column level. These functions use `INFO.VIEW.TABLES()` and `INFO.VIEW.COLUMNS()` to verify metadata visibility based on role permissions.

**Note:** OLS testing requires running tests with "View as" role context in Power BI Desktop. When a role has `metadataPermission: none` on a table or `columnPermission: none` on a column, the object will not appear in metadata queries.

##### Table-Level OLS

- `PQL.Assert.OLS.TableShouldBeHidden(testName, tableName)` - Asserts that a table is hidden from metadata (OLS `metadataPermission: none`). Use this when a role should NOT have access to view or query a table. The test passes when the table does not appear in `INFO.VIEW.TABLES()`.
- `PQL.Assert.OLS.TableShouldBeVisible(testName, tableName)` - Asserts that a table is visible in metadata (OLS allows access). Use this when a role SHOULD have access to view or query a table. The test passes when the table appears in `INFO.VIEW.TABLES()`.

##### Column-Level OLS

- `PQL.Assert.OLS.ColumnShouldBeHidden(testName, tableName, columnName)` - Asserts that a column is hidden from metadata (OLS `columnPermission: none`). Use this when a role should NOT have access to view or query a column. The test passes when the column does not appear in `INFO.VIEW.COLUMNS()`.
- `PQL.Assert.OLS.ColumnShouldBeVisible(testName, tableName, columnName)` - Asserts that a column is visible in metadata (OLS allows access). Use this when a role SHOULD have access to view or query a column. The test passes when the column appears in `INFO.VIEW.COLUMNS()`.

**OLS Testing Example:**
```dax
// Test OLS for West role (metadataPermission: none on Admin Table)
// Run with: Modeling tab → Security → View as → West
DEFINE
    FUNCTION OLS_West.ANY.Tests = () =>
    UNION(
        PQL.Assert.OLS.TableShouldBeHidden("West: Admin Table should be hidden", "Admin Table"),
        PQL.Assert.OLS.ColumnShouldBeHidden("West: Hidden Column should be hidden", "Hidden Column Table", "Hidden Column"),
        PQL.Assert.OLS.TableShouldBeVisible("West: Groups table should be visible", "Groups")
    )

EVALUATE OLS_West.ANY.Tests()
```

**OLS Configuration Example (TMDL):**
```tmdl
role West
    modelPermission: read
    
    tablePermission Admin Table = none
    
    tablePermission 'Hidden Column Table' = read
        columnPermission 'Hidden Column' = none
```

### Test Discovery

PQL.Assert provides two pairs of test discovery functions. The **V1 functions** are designed for use with Power Automate and any caller that does not support `INFO` DAX functions. The **V2 functions** return full metadata but require a context where `INFO.USERDEFINEDFUNCTIONS` and `INFO.ANNOTATIONS` are supported (e.g., DAX Query View, the Power BI MCP).

> ⚠️ **Power Automate compatibility:** The `V2` functions use `INFO.USERDEFINEDFUNCTIONS` and `INFO.ANNOTATIONS`, which are **not supported** in the Power Automate **Execute Dataset Query** action. Use the V1 functions (`RetrieveTests` / `RetrieveTestsByEnvironment`) when calling from Power Automate.

#### V1 — Power Automate compatible

- `PQL.Assert.RetrieveTests()` - Returns all test functions (ending with .Test or .Tests) as a single `[Name]` column. Uses only `INFO.FUNCTIONS`; safe for Power Automate.
- `PQL.Assert.RetrieveTestsByEnvironment([environment])` - Returns tests filtered by environment (e.g., "DEV", "TEST", "PROD") matching `.{ENV}.` or `.ANY.` in function names. Case-insensitive. Optional `environment` parameter defaults to `""` (returns all tests if omitted or blank). Uses only `INFO.FUNCTIONS`; safe for Power Automate.

#### V2 — Full metadata (not for Power Automate)

- `PQL.Assert.RetrieveTestsV2()` - Returns all test functions with full metadata columns (`[Name]`, `[Description]`, `[PQLAssert_ImpersonatedUserName]`, `[PQLAssert_RoleName]`). Uses `INFO.USERDEFINEDFUNCTIONS` and `INFO.ANNOTATIONS`. **Not compatible with Power Automate.**
- `PQL.Assert.RetrieveTestsByEnvironmentV2([environment])` - Returns tests filtered by environment with full metadata. Case-insensitive. Optional `environment` parameter defaults to `""` (returns all tests if omitted or blank). **Not compatible with Power Automate.**

### Best Practice Validations

PQL.Assert includes built-in semantic model validation functions based on Best Practice Analyzer rules. These functions help identify common issues and anti-patterns in your Power BI models.

> **Note:** Additional validation rules are being added continuously. This is an initial set covering the most critical model health checks.

#### Error Prevention

- `PQL.Assert.BP.ShouldHaveSameDataTypeInRelationships()` - Validates that relationship columns have matching data types (avoiding int64→decimal relationships)
- `PQL.Assert.BP.CheckErrorPrevention()` - Runs all error prevention checks

#### Formatting

- `PQL.Assert.BP.ShouldProvideFormatStringForMeasures()` - Validates that visible measures have format strings assigned
- `PQL.Assert.BP.ShouldNotSummarizeNumericColumns()` - Validates that numeric columns have SummarizeBy set to None
- `PQL.Assert.BP.ShouldMarkRowLabels()` - Validates that visible tables have at least one column marked as a row label (IsDefaultLabel = true), helping Power BI Copilot identify primary identifiers
- `PQL.Assert.BP.ShouldMarkPrimaryKeys()` - Validates that columns on the "One" side of relationships have the IsKey property set to true
- `PQL.Assert.BP.ShouldHideFactTableColumns()` - Validates that numeric columns used in aggregation measures are hidden from end users
- `PQL.Assert.BP.CheckFormatting()` - Runs all formatting checks

#### DAX Expressions

- `PQL.Assert.BP.ShouldUseFullyQualifiedColumnReferences()` - Validates that column references use Table[Column] format
- `PQL.Assert.BP.ShouldUseTreatAsInsteadOfIntersect()` - Validates that measures use TREATAS instead of INTERSECT for better performance
- `PQL.Assert.BP.CheckDAXExpressions()` - Runs all DAX expression checks

#### Maintenance

- `PQL.Assert.BP.ShouldRemoveUnnecessaryMeasures()` - Validates that hidden measures not referenced by any DAX expressions are flagged for removal
- `PQL.Assert.BP.ShouldHaveObjectDescriptions()` - Validates that visible tables, measures, and columns have descriptions assigned
- `PQL.Assert.BP.ShouldRemoveUnnecessaryColumns()` - Validates that hidden columns not referenced by any DAX expressions, relationships, hierarchy levels, or Sort By-properties are flagged for removal
- `PQL.Assert.BP.CheckMaintenance()` - Runs all maintenance checks

#### Performance

- `PQL.Assert.BP.ShouldAvoidBiDirectionalOnHighCardinalityColumn()` - Validates bi-directional relationships on high-cardinality columns (>1M distinct values)
- `PQL.Assert.BP.ShouldRemoveAutoDateTable()` - Validates that auto-date tables are disabled
- `PQL.Assert.BP.ShouldAvoidFloatingPointDataTypes()` - Validates that columns avoid Number (Double) data type
- `PQL.Assert.BP.ShouldSetIsAvailableInMdxFalseOnNonAttributeColumns()` - Validates IsAvailableInMdx setting on non-attribute columns
- `PQL.Assert.BP.CheckPerformance()` - Runs all performance checks

**Example Usage:**
```dax
// Run individual validation
EVALUATE PQL.Assert.BP.ShouldProvideFormatStringForMeasures()

// Run all checks in a category
EVALUATE PQL.Assert.BP.CheckFormatting()
EVALUATE PQL.Assert.BP.CheckPerformance()

// Combine all best practice checks
EVALUATE UNION(
    PQL.Assert.BP.CheckErrorPrevention(),
    PQL.Assert.BP.CheckFormatting(),
    PQL.Assert.BP.CheckDAXExpressions(),
    PQL.Assert.BP.CheckMaintenance(),
    PQL.Assert.BP.CheckPerformance()
)
```

## 🏗️ Workspace Governance & Environments

### Environment Types

**DEV (Development)**
- Static/parameterized data for stable testing
- Known baseline data state
- Focus on calculation and logic validation

**TEST**
- Live data for client/stakeholder validation
- Pre-production environment
- Content and schema validation

**PROD (Production)**
- Live production data
- Health checks and data drift detection
- Continuous monitoring

**ANY**
- Tests that apply across all environments
- Universal validation rules

### Test Types by Environment

| Test Category | DEV | TEST | PROD | Description |
|---------------|-----|------|------|-------------|
| **Testing Calculations** | ✓ | | | Validate measures, calculated columns, edge cases |
| **Testing Content** | ✓ | ✓ | ✓ | Row counts, data quality, value validation |
| **Testing Schema** | ✓ | ✓ | ✓ | Table/column existence, relationships, data types |

## 🧪 Writing and Running Tests

### Standard Schema

All test functions must return results following the DAX Query View Testing Pattern standard schema:

| Column Name | Type | Description |
|-------------|------|-------------|
| TestName | String | Description of the test being conducted |
| Expected | Any | What the test should result in (hardcoded value or Boolean) |
| Actual | Any | The result of the test under the current dataset |
| Passed | Boolean | True if expected matches actual, otherwise false |

### Tab Naming Convention

Follow the pattern: `[name].[environment].test(s)`

**Format Guidelines:**
- `[name]` - Keep to 15-20 characters for tab readability
- `[environment]` - DEV, TEST, PROD, or ANY
- Use `.tests` for multiple assertions, `.test` for single assertion

**Examples:**
- `DataQuality.DEV.Tests` - Data quality tests for development
- `Schema.ANY.Tests` - Schema validation for all environments
- `Revenue.PROD.Tests` - Revenue calculation health checks for production
- `Customers.TEST.Tests` - Customer data validation for testing environment

### Creating Test Functions

#### Testing Calculations (DEV Environment)
```dax
DEFINE
	FUNCTION BusinessLogic.DEV.Tests = () =>
	UNION (
		PQL.Assert.ShouldEqual("Revenue calculation with positive sales", 15000, [Total Revenue]),
		PQL.Assert.ShouldEqual("Revenue calculation with zero sales", 0, [Total Revenue Zero Case]),
		PQL.Assert.ShouldBeNull("Revenue calculation with blank sales", [Total Revenue Blank Case]),
		PQL.Assert.ShouldEqual("Basic math validation", 4, 2+2)
	)

EVALUATE BusinessLogic.DEV.Tests()
```

#### Testing Content (Any Environment)
```dax
DEFINE
	FUNCTION DataContent.ANY.Tests = () =>
	UNION (
		PQL.Assert.Tbl.ShouldHaveRows("Fact table has data", 'Sales'),
		PQL.Assert.Col.NullCountShouldBeAtMost("Customer ID not null", 'Customers', 'Customers'[CustomerID], 0),
		PQL.Assert.ShouldBeGreaterThan("Fact table row count threshold", 1000, COUNTROWS('Sales')),
		PQL.Assert.Col.NullCountShouldBeAtMost("Order date not null", 'Orders', 'Orders'[OrderDate], 0)
	)

EVALUATE DataContent.ANY.Tests()
```

#### Testing Schema (Any Environment)
```dax
DEFINE
	FUNCTION Schema.ANY.Tests = () =>
	UNION (
		PQL.Assert.Tbl.ShouldExist("Sales table exists", "Sales"),
		PQL.Assert.Col.ShouldExist("Customer ID column exists", "Customers", "CustomerID"),
		PQL.Assert.Relationship.ShouldExist("Sales-Customer relationship", "Sales", "CustomerID", "Customers", "CustomerID"),
		PQL.Assert.Relationship.ShouldExist("Sales-Customer is Many-to-One, single direction", "Sales", "CustomerID", "Customers", "CustomerID", "Many", "One", "OneDirection")
	)

EVALUATE Schema.ANY.Tests()
```

#### Advanced Measure Testing (DEV Environment)
```dax
DEFINE 

	FUNCTION Measures.DEV.Tests = () =>
    VAR __DS0FilterTable = 
        TREATAS({TRUE}, 'TestData'[IsActive])

    VAR _Test1 =
        SUMMARIZECOLUMNS(
            __DS0FilterTable,
            "CountID", IGNORE(CALCULATE(COUNTA('TestData'[ID])))
        )

	RETURN PQL.Assert.ShouldEqual("ShouldBeEqual to 3", 3, _Test1)

EVALUATE Measures.DEV.Tests()
```

### Discovering Tests

Use the test discovery functions to find all available test functions:

```dax
// Find all test functions
EVALUATE PQL.Assert.RetrieveTests()

// Find tests by environment (recommended approach)
EVALUATE PQL.Assert.RetrieveTestsByEnvironment("DEV")   // Returns .DEV. and .ANY. tests
EVALUATE PQL.Assert.RetrieveTestsByEnvironment("TEST")  // Returns .TEST. and .ANY. tests
EVALUATE PQL.Assert.RetrieveTestsByEnvironment("PROD")  // Returns .PROD. and .ANY. tests

// Optional parameter - omit to get all tests
EVALUATE PQL.Assert.RetrieveTestsByEnvironment()        // Returns all tests (same as RetrieveTests)
EVALUATE PQL.Assert.RetrieveTestsByEnvironment("")      // Returns all tests (same as RetrieveTests)

// Case-insensitive - these are equivalent
EVALUATE PQL.Assert.RetrieveTestsByEnvironment("dev")
EVALUATE PQL.Assert.RetrieveTestsByEnvironment("Dev")
EVALUATE PQL.Assert.RetrieveTestsByEnvironment("DEV")

// Custom environments are supported
EVALUATE PQL.Assert.RetrieveTestsByEnvironment("UAT")      // Returns .UAT. and .ANY. tests
EVALUATE PQL.Assert.RetrieveTestsByEnvironment("STAGING")  // Returns .STAGING. and .ANY. tests

// Manual filtering (alternative approach)
EVALUATE FILTER(PQL.Assert.RetrieveTests(), CONTAINSSTRING([FUNCTION_NAME], ".DEV."))
EVALUATE FILTER(PQL.Assert.RetrieveTests(), CONTAINSSTRING([FUNCTION_NAME], ".PROD."))
EVALUATE FILTER(PQL.Assert.RetrieveTests(), CONTAINSSTRING([FUNCTION_NAME], ".ANY."))
```

This returns a table of all functions ending with `.Test` or `.Tests`, making it easy to identify and run your test suites by environment.

### Running All Tests

After discovering tests, you can run them individually or create environment-specific test runners:

```dax
DEFINE
	// Run all DEV tests
	FUNCTION DEV.AllTests = () =>
	UNION (
		BusinessLogic.DEV.Tests(),
		DataQuality.DEV.Tests()
	)
	
	// Run all production health checks
	FUNCTION PROD.HealthChecks = () =>
	UNION (
		DataContent.ANY.Tests(),
		Schema.ANY.Tests()
	)

EVALUATE DEV.AllTests()
EVALUATE PROD.HealthChecks()
```

### Test Validation

Use the built-in validation layer to check if test expectations match results:

```dax
// Include "should pass" or "should fail" in test names for automatic validation
VAR _Results = Example.Tests()
VAR _Validation = 
    ADDCOLUMNS(
        _Results,
        "ExpectedToPass", CONTAINSSTRING([TestName], "should pass"),
        "ValidationStatus", 
        IF(
            CONTAINSSTRING([TestName], "should pass") && [Passed] = TRUE, "✅ CORRECT",
            IF(CONTAINSSTRING([TestName], "should fail") && [Passed] = FALSE, "✅ CORRECT", "❌ MISMATCH")
        )
    )

EVALUATE _Validation
```

## 💡 Best Practices

### Model-Independent Assertions

Reusable PQL.Assert functions should avoid assumptions about the caller's semantic model unless the function is explicitly validating named model metadata. In particular, functions that accept TABLE or TABLE EXPR parameters should not depend on hard-coded parameter column names such as `[Name]` or `[Value]`.

Prefer these patterns:
- Use row-count operations such as `COUNTROWS(tableRef)` when the assertion only needs table shape.
- Accept comma-separated STRING lists for simple schema expectations such as table, column, measure, or perspective membership.
- When comparing table expressions, normalize each side with `SELECTCOLUMNS` and an explicit projected value column before using `EXCEPT` or `INTERSECT`.
- Treat joins and filters over `INFO.*` metadata separately from user-supplied TABLE parameters. Metadata functions have known schemas; caller-provided TABLE parameters do not.

Before releasing a new reusable assertion, audit every TABLE parameter for direct column references and add tests that exercise a differently named input column when practical.

### Test Naming

- Use descriptive test names that explain what is being tested
- Include "should pass" or "should fail" for validation automation
- Group related tests in functions ending with `.Tests`

### Test Organization

- Create separate test functions for different areas (data validation, business logic, etc.)
- Use meaningful function names like `DataQuality.Tests`, `Calculations.Tests`
- Keep tests focused and atomic - test one thing per assertion

### Example Test Structure

```dax
DEFINE
	// Content validation for any environment
	FUNCTION DataQuality.ANY.Tests = () =>
	UNION (
		// Null checks
		PQL.Assert.Col.NullCountShouldBeAtMost("Data Quality: Customer ID should not be null", 'Customers', 'Customers'[CustomerID], 0),
		PQL.Assert.Col.NullCountShouldBeAtMost("Data Quality: Order date should not be null", 'Orders', 'Orders'[OrderDate], 0),
		
		// Uniqueness checks
		PQL.Assert.Col.ShouldBePrimaryKey("Data Quality: Customer ID should be unique", 'Customers'[CustomerID]),
		
		// Range checks
		PQL.Assert.ShouldBeBetween("Data Quality: Order amount in valid range", 0, 1000000, MAX('Orders'[Amount]))
	)
	
	// Calculation validation for development only
	FUNCTION BusinessLogic.DEV.Tests = () =>
	UNION (
		// Calculation validation with static data
		PQL.Assert.ShouldEqual("Business Logic: Total sales calculation", 15000, [Total Sales]),
		PQL.Assert.ShouldBeGreaterThan("Business Logic: Growth rate positive", 0, [Growth Rate])
	)
	
	// Schema validation for any environment
	FUNCTION Schema.ANY.Tests = () =>
	UNION (
		PQL.Assert.Tbl.ShouldExist("Schema: Customers table exists", "Customers"),
		PQL.Assert.Col.ShouldExist("Schema: CustomerID column exists", "Customers", "CustomerID")
	)

// Run environment-specific test suites
EVALUATE DataQuality.ANY.Tests()
EVALUATE BusinessLogic.DEV.Tests()

// Discover all available test functions
EVALUATE PQL.Assert.RetrieveTests()

// Discover tests by environment
EVALUATE FILTER(PQL.Assert.RetrieveTests(), CONTAINSSTRING([FUNCTION_NAME], ".DEV."))
```

### Membership Validation Examples

The `ValuesShouldBeInSet` and `ValuesShouldNotBeInSet` functions are model-independent, requiring explicit table and column references:

```dax
// Validate column values against an allowed set
VAR _AllowedCategories = {"A", "B", "C"}
VAR _CategoryTest = PQL.Assert.Col.ValuesShouldBeInSet(
    "Category values should be in allowed set", 
    'Products'[Category], 
    _AllowedCategories, 
    [Value]
)

// Validate column values are not in a banned set
VAR _BannedStatuses = {"Deleted", "Invalid", "Archived"}
VAR _StatusTest = PQL.Assert.Col.ValuesShouldNotBeInSet(
    "Status should not be in banned set", 
    'Orders'[Status], 
    _BannedStatuses, 
    [Value]
)

// Using a reference table
VAR _ValidCountries = PQL.Assert.Col.ValuesShouldBeInSet(
    "Countries should be valid", 
    'Sales'[Country], 
    'ValidCountries',  // Reference table
    'ValidCountries'[CountryCode]  // Column to compare against
)
```

## Documentation

- See the `lib/functions.tmdl` file for the functions included in this library.
- See the `manifest.daxlib` file for the library metadata and configuration.

## License

This project is licensed under the MIT License.
