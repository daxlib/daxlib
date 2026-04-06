# "Patou Tips" financial package of functions for project's profitabilty calculation

## About this financial package...
This package of 8 DAX functions is designed to provide access to **key performance indicators (KPIs)** representative of project profitability, such as **IRR, NPV, and payback**. It also allows for the calculation of the **metrics** such as **DF, FCF, and DCF**.

| Functions                 	      | What it is?                                                  |
| ------------------------------------|--------------------------------------------------------------|
| **DF.Patou.Tips**                   | Calculate DF (Discounted Factor) by period                   |
| **FCF.Patou.Tips**                  | Calculate FCF (Discounted Free Cash Flow) by period          |
| **DCF.Patou.Tips**                  | Calculate DCF (Discounted Cash Flow) by period               |
| **IRR.Patou.Tips**                  | Calculate IRR (Internal Rate Return) by period               |
| **NPV.Patou.Tips**                  | Calculate NPV (Net Present Value) by period                  |
| **NPV.Total.Patou.Tips**            | Calculate NPV (Net Present Value) at end of period     	     |
| **Payback.Year.Patou.Tips**         | Calculate the year of the project payback                    |
| **Payback.Nb.Year.Patou.Tips**      | Calculate the number of year of the project payback          |

Functions are created to be used independently, in whole or in part. Functions are not linked to each other by code. 
Each function generates an value for each period used to calculate the profitability of a project. These values can be displayed as a matrix or a graph for example.

<img width="1177" height="638" alt="image" src="https://github.com/user-attachments/assets/b038d038-c554-4030-b743-2f82b9aa9b9f" />

---------------------------------------------------------------------

## How it works?

### 1 - Parameters

📌 To use this function, some measures and values are required:
- **Revenue_measure** is the revenue expected of the project
- **Cost_measure** is the cost expected of the project
- **Investment_measure** is the investment expected of the project
- **Year_date_of_calendar_table** refers to the year of the calendar table or fact table
- **Discounted_rate** is a decimal value representing a percentage. For example, 0.101 for 10.1%. It can be based on the WACC, inflation rate, financial internal rate of return, requirement of the project...

> **Put Costs and Investment measures in negative value** 
  

### 2 - Usage

✅ Insert the function code (see next chapter "3 - Code") into the TMDL view.

✏️ Create a measure that calls one of the "Patou.Tips" functions, for example, write the word "Patou". The function-specific instructions are also summarized in the pop-up window that appears when the measure is inserted.

<img width="490" height="189" alt="image" src="https://github.com/user-attachments/assets/19a0d463-576a-4c51-a66a-30d29647fd59" />

### 3 - Code
```DAX
DEFINE
/// Net Present Value (NPV). This is the cumulative Discounted Cash Flow (DCF), for each period of the project. NPV evaluates the profitability of an investment by comparing the present value of expected future cash flows to the initial investment. Put Costs and Investment in negative value. The Discounted Rate is a decimal value representing a percentage. For example, 0.101 for 10.1%.

FUNCTION NPV.Patou.Tips =
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

---------------------------------------------------------------------

## Financial explanations

The **Net Present Value (NPV)** is the cumulative sum of Discounted Free Cash Flow (DCF). See also the "DCF.Patou.Tips" function to calculate the Discounted Free Cash Flow. The NPV is useful for calculating the profitability of a project. NPV evaluates the profitability of an investment by comparing the present value of expected future cash flows to the initial investment. When the NPV is positive, the project create value to by generating revenues exceed the costs, once they are discounted.

---------------------------------------------------------------------

## License

MIT License - free to use, modify, and distribute with proper attribution.
© Patou Tips - For support or contributions, contact the author.

