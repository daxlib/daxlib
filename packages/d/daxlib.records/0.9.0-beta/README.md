# DaxLib.Records

DaxLib.Records is a DAX User-Defined Functions (UDF) library designed to facilitate the creation and management of records in DAX. It provides a set of functions that allow you to create new records with varying numbers of parameters, making it easier to work with structured data in your DAX expressions.


## Usage

Use the `NewRecordValue` function to create records with 1 parameter that consists of a name and a value.
You can add other parameters to an existing record using the `AddRecordValue` function, which takes an existing record and a new name-value pair to add to it. This allows you to build up records incrementally.
```
// Create a new record with 1 parameter
VAR _Step1 = DaxLib.Records.NewRecordValue ( "ProductCode", "SKU-7890" )

// Add more parameters to the existing record
VAR _Step2 = DaxLib.Records.AddRecordValue ( _Step1, "Quantity", "150" )
VAR _Step3 = DaxLib.Records.AddRecordValue ( _Step2, "Warehouse", "Seattle" )
VAR _Step4 = DaxLib.Records.AddRecordValue ( _Step3, "Priority", "High" )
VAR _Sample = DaxLib.Records.AddRecordValue ( _Step4, "SupplierID", "SUP-4521" )
```

To make the code shorter when you create a record, use `NewRecordValueN` functions to create records with 2 to 20 parameters, where N is the number of parameters. Each parameter consists of a name and a value. For example, `NewRecordValue3` allows you to create a record with three parameters:
```
// Create a new record with 5 parameters
VAR _Sample = DaxLib.Records.NewRecordValue5 (
    "ProductCode", "SKU-7890",
    "Quantity", "150",
    "Warehouse", "Seattle",
    "Priority", "High",
    "SupplierID", "SUP-4521"
)
```
Use `GetRecordValue` and `GetRecordValueDefault` to extract values from the record.
- `GetRecordValue` retrieves the value of a parameter by its name and it raises an error if the parameter is not found.
- `GetRecordValueDefault` does the same but allows you to specify a default value to return if the parameter is not found, without raising an error.
- `HasRecordValue` checks for the existence of a parameter.

```
// Extract values from the record using GetRecordValue (error if not found)
VAR ProductCode = DaxLib.Records.GetRecordValue(_Sample, "ProductCode")
VAR Quantity = DaxLib.Records.GetRecordValue(_Sample, "Quantity")
VAR Warehouse = DaxLib.Records.GetRecordValue(_Sample, "Warehouse")
VAR Priority = DaxLib.Records.GetRecordValue(_Sample, "Priority")
VAR SupplierID = DaxLib.Records.GetRecordValue(_Sample, "SupplierID")

// Extract values from the record using GetRecordValueDefault (no error, default value if not found)   
VAR NotExistent = DaxLib.Records.GetRecordValueDefault(_Sample, "NotExistent", "custom")

// Check for the existence of a parameter using HasRecordValue
VAR HasNotExistent = DaxLib.Records.HasRecordValue(_Sample, "NonExistent")

```
The following code shows the result obtained in the variable that extracts the values of the parameters. The first row contains the original record, and the second row contains a string with the extracted values and displays the default value and existence check results.

```
EVALUATE { 
    _Sample,
    "Product: " & ProductCode &
    ", Qty: " & Quantity &
    ", Warehouse: " & Warehouse &
    ", Priority: " & Priority &
    ", Supplier: " & SupplierID &
    ", NonExistent: " & NotExistent &
    ", HasNonExistent: " & IF(HasNotExistent, "FOUND!!", "not found" )
}
```

## Performance
The implementation of the DaxLib.Records library is optimized for performance using only functions that are constant-folded in the DAX engine. This means that the functions are evaluated when the query plan is generated and do not augment the execution time at query time. The query plan will contain the final values of the records, and the functions will not be executed at runtime, ensuring that there is no performance overhead when using the library.

This is why we must use functions with a fixed number of parameters (up to 20) instead of a single function that accepts a variable number of parameters using the table constructor, which would not satisfy the constant-folding requirement and would generate a runtime overhead.

## Contributing

Contributions are welcome! Please open issues or submit pull requests on https://github.com/daxlib/dev-daxlib-records

## License

This project is licensed under the MIT License.