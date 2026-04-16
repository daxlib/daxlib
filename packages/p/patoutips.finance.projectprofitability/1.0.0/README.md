# "Patou Tips" financial package
💡 Functions for project's profitabilty calculation

## About this financial package...
This package of 8 DAX functions is designed to provide access to **key performance indicators (KPIs)** representative of project profitability, such as **IRR, NPV, and payback**. It also allows for the calculation of the **metrics** such as **DF, FCF, and DCF**.

| Functions                 	      | What it is?                                                  |
| ------------------------------------|--------------------------------------------------------------|
| PatouTips.Finance.ProjectProfitability.**DF**                   | Calculate DF (Discounted Factor) by period                   |
| PatouTips.Finance.ProjectProfitability.**FCF**                  | Calculate FCF (Discounted Free Cash Flow) by period          |
| PatouTips.Finance.ProjectProfitability.**DCF**                  | Calculate DCF (Discounted Cash Flow) by period               |
| PatouTips.Finance.ProjectProfitability.**IRR**                  | Calculate IRR (Internal Rate Return) by period               |
| PatouTips.Finance.ProjectProfitability.**NPV**                  | Calculate NPV (Net Present Value) by period                  |
| PatouTips.Finance.ProjectProfitability.**NPV.Total**            | Calculate NPV (Net Present Value) at end of period     	     |
| PatouTips.Finance.ProjectProfitability.**Payback.Year**         | Calculate the year of the project payback                    |
| PatouTips.Finance.ProjectProfitability.**Payback.Nb.Year**      | Calculate the number of year of the project payback          |

💡 Functions are created to be used independently, in whole or in part. Functions are not linked to each other by code. 
Each function generates an value for each period used to calculate the profitability of a project. These values can be displayed as a matrix or a graph for example.

<img width="1177" height="638" alt="image" src="https://github.com/user-attachments/assets/b038d038-c554-4030-b743-2f82b9aa9b9f" />

> To learn more about the financial concepts related to these functions, see the chapter "Financial Explanations".

---------------------------------------------------------------------

## How it works?

### 1 - Parameters

📌 To use these function, some measures and values are required:
- **Revenue_measure** is the revenue expected of the project
- **Cost_measure** is the cost expected of the project
- **Investment_measure** is the investment expected of the project
- **Year_date_of_calendar_table** refers to the year of the calendar table or fact table
- **Discounted_rate** is a decimal value representing a percentage. For example, 0.101 for 10.1%. It can be based on the WACC, inflation rate, financial internal rate of return, requirement of the project...

> The values ​​required for each of the functions are mentioned in the pop-up that appears when using a "Patou Tips" financial function (see next chapter "2 - Usage").
> **Put Costs and Investment measures in negative value** 
  

### 2 - Usage

✅ Insert the function code (see next chapter "3 - Code") into the TMDL view.

✏️ Create a measure that calls one of the "Patou.Tips" functions, for example, write the word "Patou". The function-specific instructions are also summarized in the pop-up window that appears when the measure is inserted.

<img width="490" height="189" alt="image" src="https://github.com/user-attachments/assets/19a0d463-576a-4c51-a66a-30d29647fd59" />

### 3 - Code
```TMDL
createOrReplace

	/// Discounted Cash Flow (DCF) is a valuation method that estimates the value of an investment, for each period of the project, based on its expected future cash flows (Cash Flow = Revenue + Costs + Investment), discounted using a discount factor. Put Costs and Investment in negative value. The Discounted Rate is a decimal value representing a percentage. For example, 0.101 for 10.1%.
	function 'PatouTips.Finance.ProjectProfitability.DCF' = ```
			
			    (
			        Revenue_measure :expr,
			        Cost_measure : expr,
			        Investment_measure : expr,
					Year_date_of_calendar_table : anyref,
					Discounted_rate : decimal
			    ) =>
			
			            VAR First_Selected_Year = 
			                CALCULATE(
			                    FIRSTNONBLANK(Year_date_of_calendar_table,Investment_measure),
			                    ALLSELECTED(Year_date_of_calendar_table)
			                ) 
			            VAR Last_Selected_Year = 
			                CALCULATE(
			                    MAX(Year_date_of_calendar_table),
			                    ALLSELECTED(Year_date_of_calendar_table)
			                )
			            VAR Selected_Year = SELECTEDVALUE(Year_date_of_calendar_table)
			
			            VAR Number_Term = Selected_Year - First_Selected_Year + 1
			
			            VAR Yearly_Discount_Rate =
			                IF(
			                    ISINSCOPE(Year_date_of_calendar_table),
			                        IF(Selected_Year>=First_Selected_Year && Selected_Year<=Last_Selected_Year,
			                            (1/(1+Discounted_Rate)^(Number_Term-1))
			                        )
			                )
			
			            RETURN (Revenue_measure+Cost_measure+Investment_measure)*Yearly_Discount_Rate
			```
		lineageTag: 54807070-1228-46ed-8407-bbb2b6cacf2a

	/// Discount Factor (DF) is a valuation method that estimates, for each period of the project, the value of one euro adjusted for inflation or to a minimum expected return on the investment, this the "Discount Rate". The Discounted Rate is a decimal value representing a percentage. For example, 0.101 for 10.1%. Note: Entering the value of an investment helps determine the first year of the project.
	function 'PatouTips.Finance.ProjectProfitability.DF' = ```
			
			    (
			        Investment_measure : expr,
					Year_date_of_calendar_table : anyref,
					Discounted_Rate : decimal
			    ) =>
			
			    VAR First_Selected_Year = 
			        CALCULATE(
			            FIRSTNONBLANK(Year_date_of_calendar_table,Investment_measure),
			            ALLSELECTED(Year_date_of_calendar_table)
			        ) 
			    VAR Last_Selected_Year = 
			        CALCULATE(
			            MAX(Year_date_of_calendar_table),
			            ALLSELECTED(Year_date_of_calendar_table)
			        )
			    VAR Selected_Year = SELECTEDVALUE(Year_date_of_calendar_table)
			
			    VAR Number_Term = Selected_Year - First_Selected_Year + 1
			
			    VAR Discount_Factor =
			        IF(
			            ISINSCOPE(Year_date_of_calendar_table),
			                IF(Selected_Year>=First_Selected_Year && Selected_Year<=Last_Selected_Year,
			                    (1/(1+Discounted_Rate)^(Number_Term-1))
			                )
			        )
			
			    RETURN Discount_Factor
			```
		lineageTag: ed899128-9112-40ed-a3f8-27b059184cbb

	/// Free Cash Flow (FCF) is a valuation method that estimates the value of an investment, for each period of the project, based on its expected future cash flows (Cash Flow = Revenue + Costs + Investment), discounted using a discount factor. Put Costs and Investment in negative value. The Discounted Rate is a decimal value representing a percentage. For example, 0.101 for 10.1%.
	function 'PatouTips.Finance.ProjectProfitability.FCF' = ```
			
			    (
			        Revenue_measure :expr,
			        Cost_measure : expr,
			        Investment_measure : expr
			    ) =>
			
			    Revenue_measure+Cost_measure+Investment_measure
			```
		lineageTag: b9468ecc-625f-412d-9e51-c4d888c0431d

	/// Internal Rate of Return (IRR). The IRR is the discount rate at wich the net present value (NPV) of a set of Discounted Cash Flows (DCF) is eaqual to zero. Put Costs and Investment in negative value. The Discounted Rate is a decimal value representing a percentage. For example, 0.101.  for 10.1%. "Fact_table" is the business data table.
	function 'PatouTips.Finance.ProjectProfitability.IRR' = ```
			
			    (
			        Revenue_measure :expr,
			        Cost_measure : expr,
			        Investment_measure : expr,
					Year_date_of_calendar_table : anyref,
			        Fact_table : table,
					Year_date_of_fact_table : anyref
			    ) =>
			
			VAR First_Selected_Year = 
			    CALCULATE(
			        FIRSTNONBLANK(Year_date_of_calendar_table,Investment_measure),
			        ALLSELECTED(Year_date_of_calendar_table)
			    ) 
			VAR Last_Selected_Year = 
			    CALCULATE(
			        MAX(Year_date_of_calendar_table),
			        ALLSELECTED(Year_date_of_calendar_table)
			    )
			
			RETURN
			CALCULATE(
			    XIRR(Fact_table,Revenue_measure+Cost_measure+Investment_measure,Year_date_of_fact_table,,0),
			    FILTER(
			        'Dim Date',
			        'Dim Date'[Year] >= First_Selected_Year && 'Dim Date'[Year] <= Last_Selected_Year))
			```
		lineageTag: a666fdee-7d48-4386-a704-3687b4de4d00

	/// Net Present Value (NPV). This is the cumulative Discounted Cash Flow (DCF), for each period of the project. NPV evaluates the profitability of an investment by comparing the present value of expected future cash flows to the initial investment. Put Costs and Investment in negative value. The Discounted Rate is a decimal value representing a percentage. For example, 0.101 for 10.1%.
	function 'PatouTips.Finance.ProjectProfitability.NPV' = ```
			
			    (
			        Revenue_measure :expr,
			        Cost_measure : expr,
			        Investment_measure : expr,
					Year_date_of_calendar_table : anyref,
					Discounted_rate : decimal
			    ) =>
			
			VAR First_Selected_Year = 
			    CALCULATE(
			        FIRSTNONBLANK(Year_date_of_calendar_table,Investment_measure),
			        ALLSELECTED(Year_date_of_calendar_table)
			    ) 
			VAR Last_Selected_Year = 
			    CALCULATE(
			        MAX(Year_date_of_calendar_table),
			        ALLSELECTED(Year_date_of_calendar_table)
			    )
			VAR Selected_Year = SELECTEDVALUE(Year_date_of_calendar_table)
			VAR Number_Term = Selected_Year - First_Selected_Year + 1
			
			RETURN
			    IF(
			        Selected_Year >= First_Selected_Year,
			        SUMX(
			            FILTER(
			                ALL(Year_date_of_calendar_table),
			                Year_date_of_calendar_table <= Selected_Year && Year_date_of_calendar_table >= First_Selected_Year
			            ),
			            VAR Year = Year_date_of_calendar_table
			            VAR Term = Year - First_Selected_Year + 1
			            VAR Discount_Factor = (1 / (1 + Discounted_rate) ^ (Term - 1))
			            VAR CF =
			                CALCULATE(
			                    Revenue_measure+Cost_measure+Investment_measure,
			                    FILTER(
			                        ALL(Year_date_of_calendar_table),
			                        Year_date_of_calendar_table = Year
			                    )
			                )
			            RETURN
			                CF * Discount_Factor
			        ),
			        BLANK()
			    )
			```
		lineageTag: b874f28f-12b2-464d-9646-d23191eef941

	/// Net Present Value (NPV). This is the value of the NPV for the entire project. Useful for KPI.
	function 'PatouTips.Finance.ProjectProfitability.NPV.Total' = ```
			
			    (
			        NPV_measure :expr,
					Year_date_of_calendar_table : anyref
			    ) =>
				
			VAR Last_Selected_Year = 
			    CALCULATE(
			        MAX(Year_date_of_calendar_table),
			        ALLSELECTED(Year_date_of_calendar_table)
			    )
			
			VAR Table_NPV =
			    SELECTCOLUMNS(
			        VALUES(Year_date_of_calendar_table),
			        "Date_Table", Year_date_of_calendar_table,
			        "NPV_Table", NPV_measure
			    )
			
			RETURN
			    MAXX(
			        FILTER(Table_NPV,[Date_Table]=Last_Selected_Year),
			        [NPV_Table]
			    )
			```
		lineageTag: cb33e2c5-e3ce-46f1-9307-b1ce21b211b6

	/// Payback Number Year determines the number of years required to achieve a return on investment, so when the NPV becomes positive.
	function 'PatouTips.Finance.ProjectProfitability.Payback.Nb.Year' = ```
			
			    (
			        NPV_measure : expr,
					Year_date_of_calendar_table : anyref
			    ) =>
			
			VAR First_Selected_Year = 
			    CALCULATE(
			        MIN(Year_date_of_calendar_table),
			        ALLSELECTED(Year_date_of_calendar_table)
			    )
			
			VAR YearWithNPV =
			    ADDCOLUMNS(
			        VALUES(Year_date_of_calendar_table),
			        "NPV_Column", NPV_Measure
			    )
			
			VAR First_Year =
			    MINX(
			        FILTER(
			            YearWithNPV,
			            [NPV_Column] > 0
			        ),
			        Year_date_of_calendar_table
			    )
			RETURN IF(ISBLANK(First_Year),"No payback",First_Year-First_Selected_Year+1)
			```
		lineageTag: 45b3da81-b070-4e04-8884-4e9ef5442493

	/// "Payback Year" determines the year of return on investment, that is, the moment when the NPV becomes positive.
	function 'PatouTips.Finance.ProjectProfitability.Payback.Year' = ```
			
			    (
			        NPV_Measure : expr,
					Year_date_of_calendar_table : anyref
			    ) =>
				
			VAR YearWithNPV =
			    ADDCOLUMNS(
			        VALUES(Year_date_of_calendar_table),
			        "NPV_Column", NPV_Measure
			    )
			
			VAR First_Year =
			    MINX(
			        FILTER(
			            YearWithNPV,
			            [NPV_Column] > 0
			        ),
			        Year_date_of_calendar_table
			    )
			RETURN IF(ISBLANK(First_Year),"No payback",First_Year)
			```
		lineageTag: ce560c75-4ed7-4390-bf10-1dfadcad64a9
```


---------------------------------------------------------------------

## Financial explanations

**Discount Factor (DF)** allows to evaluate the value of one euro (or other money) according to a required rate of return and the number of term. Required rate of return is the Discounted Rate and it can be based on the WACC, the inflation rate, the financial internal rate of return… **Discount Factor =1/(1+Discounted Rate)^(#Period-1)**

**Free cash flow (FCF)** is the money a company has left over after paying its operating expenses and capital expenditures. **Free Cash Flow (FCF) = Revenue + Costs + Investment**

**Discounted cash flow (DCF)** is a valuation method that estimates the value of an investment using its expected future cash flows. **Discounted Cash Flow (DCF) = Free Cash Flow x Discount Factor**

**Internal Rate of Return (IRR)** is the discount factor at which the net present value (NPV) of a set of discounted cash flows (DCF) is equal to zero. 

**Net Present Value (NPV)** is the cumulative sum of Discounted Free Cash Flow (DCF). The NPV is useful for calculating the profitability of a project. NPV evaluates the profitability of an investment by comparing the present value of expected future cash flows to the initial investment. When the NPV is positive, the project create value to by generating revenues exceed the costs, once they are discounted.

**Payback** period is the amount of time it takes to recover the cost of an investment. Simply put, it is the length of time an investment reaches a breakeven point. A payback period relatively short indicates a rapid return on investment, that reduces risk and improves liquidity. **Payback Period = (Project Start Date - Date when NPV >= 0) +1**

---------------------------------------------------------------------

## License

MIT License - free to use, modify, and distribute with proper attribution.
© Patou Tips - For support or contributions, contact the author.

