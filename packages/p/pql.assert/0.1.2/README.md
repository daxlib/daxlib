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

- `PQL.Assert.Col.ShouldBeNull(testName, columnRef)` - Asserts all column values are NULL
- `PQL.Assert.Col.ShouldNotBeNull(testName, columnRef)` - Asserts column has non-NULL values
- `PQL.Assert.Col.ShouldBeBlank(testName, columnRef)` - Asserts all column values are empty strings
- `PQL.Assert.Col.ShouldNotBeBlank(testName, columnRef)` - Asserts column has non-empty values
- `PQL.Assert.Col.ShouldBeNullOrBlank(testName, columnRef)` - Asserts all values are NULL or empty
- `PQL.Assert.Col.ShouldNotBeNullOrBlank(testName, columnRef)` - Asserts column has content
- `PQL.Assert.Col.ShouldBeDistinct(testName, columnRef)` - Asserts all column values are unique
- `PQL.Assert.Col.ShouldExist(testName, tableName, columnName)` - Asserts column exists

### Table Assertions

- `PQL.Assert.Tbl.ShouldHaveRows(testName, tableRef)` - Asserts table has at least one row
- `PQL.Assert.Tbl.ShouldHaveRowCount(testName, tableRef, expectedRowCount)` - Asserts exact row count
- `PQL.Assert.Tbl.ShouldHaveMoreRowsThan(testName, threshold, tableToCheck)` - Asserts row count > threshold
- `PQL.Assert.Tbl.ShouldExist(testName, tableName)` - Asserts table exists

### Relationship Assertions

- `PQL.Assert.Relationship.ShouldExist(testName, fromTable, fromColumn, toTable, toColumn)` - Asserts relationship exists

### Test Discovery

- `PQL.Assert.RetrieveTests()` - Returns all test functions (ending with .test or .tests)

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

VAR __DS0FilterTable = 
	TREATAS({TRUE}, 'TestData'[IsActive])

VAR _Test1 =
	SUMMARIZECOLUMNS(
		__DS0FilterTable,
		"CountID", IGNORE(CALCULATE(COUNTA('TestData'[ID])))
	)

	FUNCTION Measures.DEV.Tests = () =>
	PQL.Assert.ShouldEqual("ShouldBeEqual to 3", 3, _Test1)

EVALUATE Measures.DEV.Tests()
```

### Discovering Tests

Use the test discovery function to find all available test functions:

```dax
// Find all test functions
EVALUATE PQL.Assert.RetrieveTests()

// Find DEV environment tests
EVALUATE FILTER(PQL.Assert.RetrieveTests(), CONTAINSSTRING([FUNCTION_NAME], ".DEV."))

// Find PROD environment tests
EVALUATE FILTER(PQL.Assert.RetrieveTests(), CONTAINSSTRING([FUNCTION_NAME], ".PROD."))

// Find tests for any environment
EVALUATE FILTER(PQL.Assert.RetrieveTests(), CONTAINSSTRING([FUNCTION_NAME], ".ANY."))
```

This returns a table of all functions ending with `.test` or `.tests`, making it easy to identify and run your test suites by environment.

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

## Documentation

- See the `lib/functions.tmdl` file for the functions included in this library.
- See the `manifest.daxlib` file for the library metadata and configuration.

## License

This project is licensed under the MIT License.
