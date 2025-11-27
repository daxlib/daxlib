A set of functions that can be used in calculated tables to generate random sample data for experimentation, prototypes, and testing. During beta, functions and parameters are likely to evolve and change dramatically.

Use the TMDL script below to create tables and relationship after importing functions

```tmdl
createOrReplace

	model Model
		defaultPowerBIDataSourceVersion: powerBI_V3

		table Orders
			lineageTag: af93d070-5967-4784-868b-81c47fe3b3fc

			column OrderNumber
				formatString: 0
				summarizeBy: none
				isNameInferred
				sourceColumn: [OrderNumber]

				annotation SummarizationSetBy = User

			column OrderLineNumber
				formatString: 0
				summarizeBy: none
				isNameInferred
				sourceColumn: [OrderLineNumber]

				annotation SummarizationSetBy = User

			column CustomerKey
				isHidden
				formatString: 0
				summarizeBy: none
				isNameInferred
				sourceColumn: [CustomerKey]

				annotation SummarizationSetBy = Automatic

			column ProductKey
				isHidden
				formatString: 0
				summarizeBy: none
				isNameInferred
				sourceColumn: [ProductKey]

				annotation SummarizationSetBy = Automatic

			column StoreKey
				isHidden
				formatString: 0
				summarizeBy: none
				isNameInferred
				sourceColumn: [StoreKey]

				annotation SummarizationSetBy = Automatic

			column Currency
				summarizeBy: none
				isNameInferred
				sourceColumn: [Currency]

				annotation SummarizationSetBy = Automatic

			column OrderDate
				formatString: General Date
				summarizeBy: none
				isNameInferred
				sourceColumn: [OrderDate]

				annotation SummarizationSetBy = Automatic

			column RequestedDeliveryDate
				formatString: General Date
				summarizeBy: none
				isNameInferred
				sourceColumn: [RequestedDeliveryDate]

				annotation SummarizationSetBy = Automatic

			column ApprovedDate
				formatString: General Date
				summarizeBy: none
				isNameInferred
				sourceColumn: [ApprovedDate]

				annotation SummarizationSetBy = Automatic

			column FulfilledDate
				formatString: General Date
				summarizeBy: none
				isNameInferred
				sourceColumn: [FulfilledDate]

				annotation SummarizationSetBy = Automatic

			column ShippedDate
				formatString: General Date
				summarizeBy: none
				isNameInferred
				sourceColumn: [ShippedDate]

				annotation SummarizationSetBy = Automatic

			column DeliveredDate
				formatString: General Date
				summarizeBy: none
				isNameInferred
				sourceColumn: [DeliveredDate]

				annotation SummarizationSetBy = Automatic

			column OrderStatus
				summarizeBy: none
				isNameInferred
				sourceColumn: [OrderStatus]

				annotation SummarizationSetBy = Automatic

			column DeliverySpeed
				formatString: 0
				summarizeBy: none
				isNameInferred
				sourceColumn: [DeliverySpeed]

				annotation SummarizationSetBy = User

			column Quantity
				formatString: 0
				summarizeBy: none
				isNameInferred
				sourceColumn: [Quantity]

				annotation SummarizationSetBy = User

			column UnitPrice
				summarizeBy: none
				isNameInferred
				sourceColumn: [UnitPrice]

				annotation SummarizationSetBy = User

				annotation PBI_FormatHint = {"isGeneralNumber":true}

			partition Orders = calculated
				mode: import
				source =
						
						DataGoblins.SampleData.Orders(
						    100,                   // Generate 100 orders
						    DATE(2025, 08, 01),    // Start date
						    DATE(2025, 10, 01),    // End date
						    100,                   // 100 unique customers
						    250,                   // 250 unique products
						    5,                     // 5 store locations
						    1337                   // Random seed
						)

			annotation PBI_Id = 0fa1598f1f52473e9dcd1377ac27b43c

		table Customers

			column CustKey
				summarizeBy: none
				isNameInferred
				sourceColumn: [CustKey]

				annotation SummarizationSetBy = Automatic

			column CustomerKey
				summarizeBy: none
				isNameInferred
				sourceColumn: [CustomerKey]

				annotation SummarizationSetBy = Automatic

			column CustomerName
				summarizeBy: none
				isNameInferred
				sourceColumn: [CustomerName]

				annotation SummarizationSetBy = Automatic

			column CustomerType
				summarizeBy: none
				isNameInferred
				sourceColumn: [CustomerType]

				annotation SummarizationSetBy = Automatic

			column CustomerSegment
				summarizeBy: none
				isNameInferred
				sourceColumn: [CustomerSegment]

				annotation SummarizationSetBy = Automatic

			column Industry
				summarizeBy: none
				isNameInferred
				sourceColumn: [Industry]

				annotation SummarizationSetBy = Automatic

			column Country
				summarizeBy: none
				isNameInferred
				sourceColumn: [Country]

				annotation SummarizationSetBy = Automatic

			column Region
				summarizeBy: none
				isNameInferred
				sourceColumn: [Region]

				annotation SummarizationSetBy = Automatic

			column City
				summarizeBy: none
				isNameInferred
				sourceColumn: [City]

				annotation SummarizationSetBy = Automatic

			column LifetimeValue
				summarizeBy: none
				isNameInferred
				sourceColumn: [LifetimeValue]

				annotation SummarizationSetBy = Automatic

			column CreditLimit
				summarizeBy: none
				isNameInferred
				sourceColumn: [CreditLimit]

				annotation SummarizationSetBy = Automatic

			column AccountOpenDate
				summarizeBy: none
				isNameInferred
				sourceColumn: [AccountOpenDate]

				annotation SummarizationSetBy = Automatic

			column Status
				summarizeBy: none
				isNameInferred
				sourceColumn: [Status]

				annotation SummarizationSetBy = Automatic

			partition Customers = calculated
				mode: import
				source =
						
						DataGoblins.SampleData.Customer(
						    'Orders'[CustomerKey], // Use customer keys from Orders table
						    0,                     // No referential integrity violations
						    FALSE,                 // No missing values
						    1337                   // Random seed
						)

			annotation PBI_Id = 0011860a694744a5b214cbc903e9b0b7

		table Dates
			dataCategory: Time

			column Value
				summarizeBy: none
				isNameInferred
				sourceColumn: [Value]

				annotation SummarizationSetBy = User

			column Date
				isKey
				summarizeBy: none
				isNameInferred
				sourceColumn: [Date]

				annotation SummarizationSetBy = Automatic

			column DateKey
				summarizeBy: none
				isNameInferred
				sourceColumn: [DateKey]

				annotation SummarizationSetBy = Automatic

			column DateFormatted
				summarizeBy: none
				isNameInferred
				sourceColumn: [DateFormatted]

				annotation SummarizationSetBy = Automatic

			column Year
				summarizeBy: none
				isNameInferred
				sourceColumn: [Year]

				annotation SummarizationSetBy = User

			column Quarter
				summarizeBy: none
				isNameInferred
				sourceColumn: [Quarter]

				annotation SummarizationSetBy = User

			column QuarterName
				summarizeBy: none
				isNameInferred
				sourceColumn: [QuarterName]

				annotation SummarizationSetBy = Automatic

			column Month
				summarizeBy: none
				isNameInferred
				sourceColumn: [Month]

				annotation SummarizationSetBy = User

			column MonthName
				summarizeBy: none
				isNameInferred
				sourceColumn: [MonthName]

				annotation SummarizationSetBy = Automatic

			column MonthNameShort
				summarizeBy: none
				isNameInferred
				sourceColumn: [MonthNameShort]

				annotation SummarizationSetBy = Automatic

			column Week
				summarizeBy: none
				isNameInferred
				sourceColumn: [Week]

				annotation SummarizationSetBy = User

			column Day
				summarizeBy: none
				isNameInferred
				sourceColumn: [Day]

				annotation SummarizationSetBy = User

			column DayOfWeek
				summarizeBy: none
				isNameInferred
				sourceColumn: [DayOfWeek]

				annotation SummarizationSetBy = User

			column DayOfYear
				summarizeBy: none
				isNameInferred
				sourceColumn: [DayOfYear]

				annotation SummarizationSetBy = User

			column DayName
				summarizeBy: none
				isNameInferred
				sourceColumn: [DayName]

				annotation SummarizationSetBy = Automatic

			column DayNameShort
				summarizeBy: none
				isNameInferred
				sourceColumn: [DayNameShort]

				annotation SummarizationSetBy = Automatic

			column MonthSort
				summarizeBy: none
				isNameInferred
				sourceColumn: [MonthSort]

				annotation SummarizationSetBy = User

			column DaySort
				summarizeBy: none
				isNameInferred
				sourceColumn: [DaySort]

				annotation SummarizationSetBy = User

			column QuarterSort
				summarizeBy: none
				isNameInferred
				sourceColumn: [QuarterSort]

				annotation SummarizationSetBy = User

			column YearMonth
				summarizeBy: none
				isNameInferred
				sourceColumn: [YearMonth]

				annotation SummarizationSetBy = Automatic

			column YearMonthName
				summarizeBy: none
				isNameInferred
				sourceColumn: [YearMonthName]

				annotation SummarizationSetBy = Automatic

			column YearQuarter
				summarizeBy: none
				isNameInferred
				sourceColumn: [YearQuarter]

				annotation SummarizationSetBy = Automatic

			column YearWeek
				summarizeBy: none
				isNameInferred
				sourceColumn: [YearWeek]

				annotation SummarizationSetBy = Automatic

			column FiscalYear
				summarizeBy: none
				isNameInferred
				sourceColumn: [FiscalYear]

				annotation SummarizationSetBy = User

			column FiscalQuarter
				summarizeBy: none
				isNameInferred
				sourceColumn: [FiscalQuarter]

				annotation SummarizationSetBy = User

			column FiscalMonth
				summarizeBy: none
				isNameInferred
				sourceColumn: [FiscalMonth]

				annotation SummarizationSetBy = User

			column IsWeekend
				summarizeBy: none
				isNameInferred
				sourceColumn: [IsWeekend]

				annotation SummarizationSetBy = Automatic

			column IsWeekday
				summarizeBy: none
				isNameInferred
				sourceColumn: [IsWeekday]

				annotation SummarizationSetBy = Automatic

			column IsMonthStart
				summarizeBy: none
				isNameInferred
				sourceColumn: [IsMonthStart]

				annotation SummarizationSetBy = Automatic

			column IsMonthEnd
				summarizeBy: none
				isNameInferred
				sourceColumn: [IsMonthEnd]

				annotation SummarizationSetBy = Automatic

			column IsQuarterStart
				summarizeBy: none
				isNameInferred
				sourceColumn: [IsQuarterStart]

				annotation SummarizationSetBy = Automatic

			column IsQuarterEnd
				summarizeBy: none
				isNameInferred
				sourceColumn: [IsQuarterEnd]

				annotation SummarizationSetBy = Automatic

			column IsYearStart
				summarizeBy: none
				isNameInferred
				sourceColumn: [IsYearStart]

				annotation SummarizationSetBy = Automatic

			column IsYearEnd
				summarizeBy: none
				isNameInferred
				sourceColumn: [IsYearEnd]

				annotation SummarizationSetBy = Automatic

			column IsToday
				summarizeBy: none
				isNameInferred
				sourceColumn: [IsToday]

				annotation SummarizationSetBy = Automatic

			column IsCurrentMonth
				summarizeBy: none
				isNameInferred
				sourceColumn: [IsCurrentMonth]

				annotation SummarizationSetBy = Automatic

			column IsCurrentQuarter
				summarizeBy: none
				isNameInferred
				sourceColumn: [IsCurrentQuarter]

				annotation SummarizationSetBy = Automatic

			column IsCurrentYear
				summarizeBy: none
				isNameInferred
				sourceColumn: [IsCurrentYear]

				annotation SummarizationSetBy = Automatic

			column IsPast
				summarizeBy: none
				isNameInferred
				sourceColumn: [IsPast]

				annotation SummarizationSetBy = Automatic

			column IsFuture
				summarizeBy: none
				isNameInferred
				sourceColumn: [IsFuture]

				annotation SummarizationSetBy = Automatic

			partition Dates = calculated
				mode: import
				source =
						
						DataGoblins.SampleData.Dates(
						    'Orders'[OrderDate],           // Earliest date column
						    'Orders'[DeliveredDate]        // Latest date column
						)

			annotation PBI_Id = 4f74222f84fa415da4af71b077859cde

		table Inventory

			column SnapshotDateKey
				formatString: 0
				summarizeBy: none
				isNameInferred
				sourceColumn: [SnapshotDateKey]

				annotation SummarizationSetBy = Automatic

			column ProductKey
				isHidden
				formatString: 0
				summarizeBy: none
				isNameInferred
				sourceColumn: [ProductKey]

				annotation SummarizationSetBy = Automatic

			column WarehouseKey
				formatString: 0
				summarizeBy: none
				isNameInferred
				sourceColumn: [WarehouseKey]

				annotation SummarizationSetBy = User

			column OnHandQuantity
				summarizeBy: none
				isNameInferred
				sourceColumn: [OnHandQuantity]

				annotation SummarizationSetBy = User

				annotation PBI_FormatHint = {"isGeneralNumber":true}

			column ReservedQuantity
				summarizeBy: none
				isNameInferred
				sourceColumn: [ReservedQuantity]

				annotation SummarizationSetBy = User

				annotation PBI_FormatHint = {"isGeneralNumber":true}

			column AvailableQuantity
				summarizeBy: none
				isNameInferred
				sourceColumn: [AvailableQuantity]

				annotation SummarizationSetBy = User

				annotation PBI_FormatHint = {"isGeneralNumber":true}

			column InTransitQuantity
				summarizeBy: none
				isNameInferred
				sourceColumn: [InTransitQuantity]

				annotation SummarizationSetBy = User

				annotation PBI_FormatHint = {"isGeneralNumber":true}

			column ProjectedQuantity
				summarizeBy: none
				isNameInferred
				sourceColumn: [ProjectedQuantity]

				annotation SummarizationSetBy = User

				annotation PBI_FormatHint = {"isGeneralNumber":true}

			column ReorderPoint
				summarizeBy: none
				isNameInferred
				sourceColumn: [ReorderPoint]

				annotation SummarizationSetBy = User

				annotation PBI_FormatHint = {"isGeneralNumber":true}

			column SafetyStockLevel
				summarizeBy: none
				isNameInferred
				sourceColumn: [SafetyStockLevel]

				annotation SummarizationSetBy = User

				annotation PBI_FormatHint = {"isGeneralNumber":true}

			column BelowReorderPoint
				formatString: """TRUE"";""TRUE"";""FALSE"""
				summarizeBy: none
				isNameInferred
				sourceColumn: [BelowReorderPoint]

				annotation SummarizationSetBy = Automatic

			column BelowSafetyStock
				formatString: """TRUE"";""TRUE"";""FALSE"""
				summarizeBy: none
				isNameInferred
				sourceColumn: [BelowSafetyStock]

				annotation SummarizationSetBy = Automatic

			column OutOfStock
				formatString: """TRUE"";""TRUE"";""FALSE"""
				summarizeBy: none
				isNameInferred
				sourceColumn: [OutOfStock]

				annotation SummarizationSetBy = Automatic

			column UnitCost
				summarizeBy: none
				isNameInferred
				sourceColumn: [UnitCost]

				annotation SummarizationSetBy = User

				annotation PBI_FormatHint = {"isGeneralNumber":true}

			column InventoryValue
				summarizeBy: none
				isNameInferred
				sourceColumn: [InventoryValue]

				annotation SummarizationSetBy = User

				annotation PBI_FormatHint = {"isGeneralNumber":true}

			partition Inventory = calculated
				mode: import
				source = ```
						
						 DataGoblins.SampleData.Inventory(
						    90,                     // 90 days of data
						    DATE(2025, 06, 01),    // Start date
						    250,                   // 250 products
						    3,                     // 3 warehouses
						    1337                   // Random seed
						)
						```

			annotation PBI_Id = 87ede604581d459581a3f60c6f11827b

		table Products

			column ProdKey
				summarizeBy: none
				isNameInferred
				sourceColumn: [ProdKey]

				annotation SummarizationSetBy = User

			column ProductKey
				summarizeBy: none
				isNameInferred
				sourceColumn: [ProductKey]

				annotation SummarizationSetBy = Automatic

			column SKU
				summarizeBy: none
				isNameInferred
				sourceColumn: [SKU]

				annotation SummarizationSetBy = Automatic

			column ProductName
				summarizeBy: none
				isNameInferred
				sourceColumn: [ProductName]

				annotation SummarizationSetBy = Automatic

			column ProductCategory
				summarizeBy: none
				isNameInferred
				sourceColumn: [ProductCategory]

				annotation SummarizationSetBy = Automatic

			column ProductSubcategory
				summarizeBy: none
				isNameInferred
				sourceColumn: [ProductSubcategory]

				annotation SummarizationSetBy = Automatic

			column Brand
				summarizeBy: none
				isNameInferred
				sourceColumn: [Brand]

				annotation SummarizationSetBy = Automatic

			column Color
				summarizeBy: none
				isNameInferred
				sourceColumn: [Color]

				annotation SummarizationSetBy = Automatic

			column Size
				summarizeBy: none
				isNameInferred
				sourceColumn: [Size]

				annotation SummarizationSetBy = Automatic

			column Weight
				summarizeBy: none
				isNameInferred
				sourceColumn: [Weight]

				annotation SummarizationSetBy = User

			column StandardCost
				summarizeBy: none
				isNameInferred
				sourceColumn: [StandardCost]

				annotation SummarizationSetBy = User

			column ListPrice
				summarizeBy: none
				isNameInferred
				sourceColumn: [ListPrice]

				annotation SummarizationSetBy = User

			column ProductStatus
				summarizeBy: none
				isNameInferred
				sourceColumn: [ProductStatus]

				annotation SummarizationSetBy = Automatic

			column LaunchDate
				summarizeBy: none
				isNameInferred
				sourceColumn: [LaunchDate]

				annotation SummarizationSetBy = Automatic

			column DiscontinuedDate
				summarizeBy: none
				isNameInferred
				sourceColumn: [DiscontinuedDate]

				annotation SummarizationSetBy = Automatic

			partition Products = calculated
				mode: import
				source =
						
						DataGoblins.SampleData.Product(
						    'Orders'[ProductKey],  // Use product keys from Orders table
						    0,                     // No referential integrity violations
						    FALSE,                 // No missing values
						    1337                   // Random seed
						)

			annotation PBI_Id = 2dc3d3d9dde648d5be3cd6639fb7cc6a

		table Stores

			column StrKey
				summarizeBy: none
				isNameInferred
				sourceColumn: [StrKey]

				annotation SummarizationSetBy = Automatic

			column StoreKey
				summarizeBy: none
				isNameInferred
				sourceColumn: [StoreKey]

				annotation SummarizationSetBy = Automatic

			column StoreCode
				summarizeBy: none
				isNameInferred
				sourceColumn: [StoreCode]

				annotation SummarizationSetBy = Automatic

			column StoreName
				summarizeBy: none
				isNameInferred
				sourceColumn: [StoreName]

				annotation SummarizationSetBy = Automatic

			column StoreRegion
				summarizeBy: none
				isNameInferred
				sourceColumn: [StoreRegion]

				annotation SummarizationSetBy = Automatic

			column StoreDistrict
				summarizeBy: none
				isNameInferred
				sourceColumn: [StoreDistrict]

				annotation SummarizationSetBy = Automatic

			column StoreType
				summarizeBy: none
				isNameInferred
				sourceColumn: [StoreType]

				annotation SummarizationSetBy = Automatic

			column StoreFormat
				summarizeBy: none
				isNameInferred
				sourceColumn: [StoreFormat]

				annotation SummarizationSetBy = Automatic

			column Country
				summarizeBy: none
				isNameInferred
				sourceColumn: [Country]

				annotation SummarizationSetBy = Automatic

			column State
				summarizeBy: none
				isNameInferred
				sourceColumn: [State]

				annotation SummarizationSetBy = Automatic

			column City
				summarizeBy: none
				isNameInferred
				sourceColumn: [City]

				annotation SummarizationSetBy = Automatic

			column PostalCode
				summarizeBy: none
				isNameInferred
				sourceColumn: [PostalCode]

				annotation SummarizationSetBy = Automatic

			column SquareFootage
				summarizeBy: none
				isNameInferred
				sourceColumn: [SquareFootage]

				annotation SummarizationSetBy = Automatic

			column EmployeeCount
				summarizeBy: none
				isNameInferred
				sourceColumn: [EmployeeCount]

				annotation SummarizationSetBy = Automatic

			column ParkingSpaces
				summarizeBy: none
				isNameInferred
				sourceColumn: [ParkingSpaces]

				annotation SummarizationSetBy = Automatic

			column StoreManager
				summarizeBy: none
				isNameInferred
				sourceColumn: [StoreManager]

				annotation SummarizationSetBy = Automatic

			column OpenDate
				summarizeBy: none
				isNameInferred
				sourceColumn: [OpenDate]

				annotation SummarizationSetBy = Automatic

			column LastRenovationDate
				summarizeBy: none
				isNameInferred
				sourceColumn: [LastRenovationDate]

				annotation SummarizationSetBy = Automatic

			column Status
				summarizeBy: none
				isNameInferred
				sourceColumn: [Status]

				annotation SummarizationSetBy = Automatic

			partition Stores = calculated
				mode: import
				source =
						
						DataGoblins.SampleData.Store(
						    'Orders'[StoreKey],    // Use store keys from Orders table
						    0,                     // No referential integrity violations
						    FALSE,                 // No missing values
						    1337                   // Random seed
						)

			annotation PBI_Id = 7415850f01034f40a7aaf900cd4a4db5

		relationship 897057e4-b826-d313-8564-78284c464a5f
			fromColumn: Inventory.ProductKey
			toColumn: Products.ProductKey

		relationship 7806b868-20de-d046-0019-7da22300f2b7
			fromColumn: Inventory.SnapshotDateKey
			toColumn: Dates.DateKey

		relationship eb1b11b9-c25a-b426-3008-1f2def9fc017
			fromColumn: Orders.CustomerKey
			toColumn: Customers.CustomerKey

		relationship fe30f283-c6f4-633e-8e6a-60e30be95fcf
			fromColumn: Orders.ProductKey
			toColumn: Products.ProductKey

		relationship 742da557-4690-d533-12b0-abbbf0772f42
			fromColumn: Orders.StoreKey
			toColumn: Stores.StoreKey

		relationship defec942-592e-b919-2f41-8e1c07a38bc6
			fromColumn: Orders.OrderDate
			toColumn: Dates.Date

		relationship 3b749645-46b8-cfe8-e49a-ea0ca943f4be
			isActive: false
			fromColumn: Orders.ApprovedDate
			toColumn: Dates.Date

		relationship 7224cee5-31ac-41fa-ddd9-258d203dcd36
			isActive: false
			fromColumn: Orders.FulfilledDate
			toColumn: Dates.Date

		relationship 9fcea753-b9d3-a435-7cbc-a67b7efe48fd
			isActive: false
			fromColumn: Orders.DeliveredDate
			toColumn: Dates.Date
```