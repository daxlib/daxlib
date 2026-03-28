# About "NPV.Patou.Tips" function

The **Net Present Value (NPV)** is the cumulative sum of Discounted Free Cash Flow (DCF). See also the "DCF.Patou.Tips" function to calculate the Discounted Free Cash Flow. The NPV is useful for calculating the profitability of a project. NPV evaluates the profitability of an investment by comparing the present value of expected future cash flows to the initial investment. When the NPV is positive, the project create value to by generating revenues exceed the costs, once they are discounted.

<img width="693" height="246" alt="image" src="https://github.com/user-attachments/assets/91e33e49-2d28-4d14-8bb3-7873f362f145" />

## How it works?
This function generates an NPV value for each period used to calculate the profitability of a project. This value can be displayed as a matrix or a graph.

📌 To use this function, some measures and values are required:
  - **Revenue_measure** is the revenue expected of the project
  - **Cost_measure** is the cost expected of the project
  - **Investment_measure** is the investment expected of the project
  - **Year_date_of_calendar_table** refers to the year shown in the calendar table or fact table
  -  **Discounted_rate** is is a decimal value representing a percentage. For example, 0.101 for 10.1%. It can be based on the WACC, inflation rate, financial internal rate of return...
  -  ⛔ **Put Costs and Investment in negative value** 
  
✏️ Create a measure that calls one of the "Patou.Tips" functions (write the word "Patou" for example), the function-specific instructions are also synthesized in the function's code.
