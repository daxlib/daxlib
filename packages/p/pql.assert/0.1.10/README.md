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

### String Assertions

- `PQL.Assert.ShouldStartWith(testName, prefix, actual)` - Asserts string starts with prefix
- `PQL.Assert.ShouldEndWith(testName, suffix, actual)` - Asserts string ends with suffix
- `PQL.Assert.ShouldContainString(testName, substring, actual)` - Asserts string contains substring
- `PQL.Assert.ShouldMatch(testName, pattern, actual)` - Asserts string matches pattern

### Column Assertions

#### Null & Blank Checks

- `PQL.Assert.Col.ShouldBeNull(testName, columnRef)` - Asserts all column values are NULL
- `PQL.Assert.Col.ShouldNotBeNull(testName, columnRef)` - Asserts column has non-NULL values
- `PQL.Assert.Col.ShouldBeBlank(testName, columnRef)` - Asserts all column values are empty strings
- `PQL.Assert.Col.ShouldNotBeBlank(testName, columnRef)` - Asserts column has non-empty values
- `PQL.Assert.Col.ShouldBeNullOrBlank(testName, columnRef)` - Asserts all values are NULL or empty
- `PQL.Assert.Col.ShouldNotBeNullOrBlank(testName, columnRef)` - Asserts column has content

#### Presence & Completeness Thresholds

- `PQL.Assert.Col.NullRateShouldBeAtMost(testName, columnRef, maxNullPct)` - Asserts null rate is at most the specified percentage
- `PQL.Assert.Col.BlankRateShouldBeAtMost(testName, columnRef, maxBlankPct)` - Asserts blank (empty string) rate is at most the specified percentage
- `PQL.Assert.Col.NullOrBlankRateShouldBeAtMost(testName, columnRef, maxPct)` - Asserts null or blank rate is at most the specified percentage
- `PQL.Assert.Col.PopulatedRateShouldBeAtLeast(testName, columnRef, minPct)` - Asserts populated (non-null, non-blank) rate is at least the specified percentage

#### Uniqueness & Key-Like Behavior

- `PQL.Assert.Col.ShouldBeDistinct(testName, columnRef)` - Asserts all column values are unique
- `PQL.Assert.Col.ShouldNotContainDuplicates(testName, columnRef)` - Asserts column contains no duplicate values (alias for ShouldBeDistinct)
- `PQL.Assert.Col.ShouldBeUniqueWithin(testName, columnRef, partitionByCol)` - Asserts column values are unique within partitions defined by another column
- `PQL.Assert.Col.ComboShouldBeDistinct(testName, columnRef1, columnRef2)` - Asserts the combination of two columns forms a distinct composite key

#### Range & Distribution Checks

- `PQL.Assert.Col.ShouldBeInRange(testName, columnRef, minIncl, maxIncl)` - Asserts all numeric column values fall within the specified inclusive range
- `PQL.Assert.Col.MinShouldBeAtLeast(testName, columnRef, minIncl)` - Asserts minimum value in column is at least the specified value
- `PQL.Assert.Col.MaxShouldBeAtMost(testName, columnRef, maxIncl)` - Asserts maximum value in column is at most the specified value
- `PQL.Assert.Col.MeanShouldBeInRange(testName, columnRef, minIncl, maxIncl)` - Asserts mean of column falls within the specified range
- `PQL.Assert.Col.PctileShouldBeInRange(testName, columnRef, pct, minIncl, maxIncl)` - Asserts specified percentile of column falls within the given range
- `PQL.Assert.Col.StdDevShouldBeAtMost(testName, columnRef, maxStdDev)` - Asserts standard deviation of column is at most the specified value
- `PQL.Assert.Col.ShareOfZerosAtMost(testName, columnRef, maxPct)` - Asserts share of zero values is at most the specified percentage
- `PQL.Assert.Col.ShareOfEvensAtLeast(testName, columnRef, minPct)` - Asserts share of even values is at least the specified percentage
- `PQL.Assert.Col.ShareOfOddsAtLeast(testName, columnRef, minPct)` - Asserts share of odd values is at least the specified percentage

#### Sign & Monotonicity

- `PQL.Assert.Col.ShouldBeNonNegative(testName, columnRef)` - Asserts all values in column are non-negative (>= 0)
- `PQL.Assert.Col.ShouldBePositive(testName, columnRef)` - Asserts all values in column are strictly positive (> 0)
- `PQL.Assert.Col.ShouldBeNonZero(testName, columnRef)` - Asserts no values in column are zero
- `PQL.Assert.Col.ShouldBeMonotonicIncreasing(testName, columnRef, orderedByCol)` - Asserts values monotonically strictly increase when ordered by another column
- `PQL.Assert.Col.ShouldBeMonotonicNonDecreasing(testName, columnRef, orderedByCol)` - Asserts values monotonically non-decrease when ordered by another column
- `PQL.Assert.Col.ShouldBeMonotonicDecreasing(testName, columnRef, orderedByCol)` - Asserts values monotonically strictly decrease when ordered by another column

#### Categorical, Membership & Format

- `PQL.Assert.Col.ValuesShouldBeInSet(testName, columnRef, allowedValuesTable, allowedValuesColumn)` - Asserts all column values are in the specified allowed set (model-independent: explicitly specify table and column)
- `PQL.Assert.Col.ValuesShouldNotBeInSet(testName, columnRef, bannedValuesTable, bannedValuesColumn)` - Asserts no column values are in the specified banned set (model-independent: explicitly specify table and column)
- `PQL.Assert.Col.DistinctCountShouldBeAtMost(testName, columnRef, maxDistinct)` - Asserts distinct count of values is at most the specified number
- `PQL.Assert.Col.DistinctCountShouldBeAtLeast(testName, columnRef, minDistinct)` - Asserts distinct count of values is at least the specified number
- `PQL.Assert.Col.TextShouldHaveNoLeadingOrTrailingSpaces(testName, columnRef)` - Asserts no text values have leading or trailing spaces
- `PQL.Assert.Col.TextShouldBeTrimmed(testName, columnRef)` - Asserts all text values are trimmed (alias for TextShouldHaveNoLeadingOrTrailingSpaces)
- `PQL.Assert.Col.TextCaseShouldBeUpper(testName, columnRef)` - Asserts all text values are uppercase
- `PQL.Assert.Col.TextCaseShouldBeLower(testName, columnRef)` - Asserts all text values are lowercase
- `PQL.Assert.Col.TextLengthShouldBeInRange(testName, columnRef, minLen, maxLen)` - Asserts text lengths fall within the specified range

#### Schema & Existence

- `PQL.Assert.Col.ShouldExist(testName, tableName, columnName)` - Asserts column exists

### Table Assertions

#### Row Checks

- `PQL.Assert.Tbl.ShouldHaveRows(testName, tableRef)` - Asserts table has at least one row
- `PQL.Assert.Tbl.ShouldHaveRowCount(testName, tableRef, expectedRowCount)` - Asserts exact row count
- `PQL.Assert.Tbl.ShouldHaveMoreRowsThan(testName, threshold, tableToCheck)` - Asserts row count > threshold

#### Schema & Column Shape

- `PQL.Assert.Tbl.ShouldExist(testName, tableName)` - Asserts table exists
- `PQL.Assert.Tbl.ShouldHaveColumns(testName, tableRef, columnNamesList)` - Asserts table has all specified columns (comma-separated list)
- `PQL.Assert.Tbl.ShouldNotHaveExtraColumns(testName, tableRef, allowedColumnNamesList)` - Asserts table does not have extra columns beyond the allowed list (comma-separated)
- `PQL.Assert.Tbl.ColumnDataTypeShouldBe(testName, tableRef, columnName, expectedType)` - Asserts column has the expected data type

### Relationship Assertions

- `PQL.Assert.Relationship.ShouldExist(testName, fromTable, fromColumn, toTable, toColumn)` - Asserts relationship exists

### Test Discovery

- `PQL.Assert.RetrieveTests()` - Returns all test functions (ending with .Test or .Tests)
- `PQL.Assert.RetrieveTestsByEnvironment(environment)` - Returns tests filtered by environment (e.g., "DEV", "TEST", "PROD") matching `.{ENV}.` or `.ANY.` in function names. Case-insensitive. Returns all tests if environment is blank.

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
		PQL.Assert.Col.ShouldNotBeNull("Customer ID not null", 'Customers'[CustomerID]),
		PQL.Assert.ShouldBeGreaterThan("Fact table row count threshold", 1000, COUNTROWS('Sales')),
		PQL.Assert.Col.ShouldNotBeNull("Order date not null", 'Orders'[OrderDate])
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
		PQL.Assert.Relationship.ShouldExist("Sales-Customer relationship", "Sales", "CustomerID", "Customers", "CustomerID")
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

// Case-insensitive - these are equivalent
EVALUATE PQL.Assert.RetrieveTestsByEnvironment("dev")
EVALUATE PQL.Assert.RetrieveTestsByEnvironment("Dev")
EVALUATE PQL.Assert.RetrieveTestsByEnvironment("DEV")

// Custom environments are supported
EVALUATE PQL.Assert.RetrieveTestsByEnvironment("UAT")      // Returns .UAT. and .ANY. tests
EVALUATE PQL.Assert.RetrieveTestsByEnvironment("STAGING")  // Returns .STAGING. and .ANY. tests

// Blank returns all tests (same as RetrieveTests)
EVALUATE PQL.Assert.RetrieveTestsByEnvironment("")

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
		PQL.Assert.Col.ShouldNotBeNull("Data Quality: Customer ID should not be null", 'Customers'[CustomerID]),
		PQL.Assert.Col.ShouldNotBeNull("Data Quality: Order date should not be null", 'Orders'[OrderDate]),
		
		// Uniqueness checks
		PQL.Assert.Col.ShouldBeDistinct("Data Quality: Customer ID should be unique", 'Customers'[CustomerID]),
		
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
