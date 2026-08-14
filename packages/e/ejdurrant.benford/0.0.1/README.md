# EJDurrant.Benford
Measure and visualise the conformance, or otherwise, of a column of numbers with Benford's Law.

## Benford.AverageDeviation
Average Deviation outputs a scalar value which is the average, over the ninety possible two-digit values, of how much the row count for each value, as a proportion of the total rows, differs from the proportion that would conform to Benford's Law. 

## Benford.DetailedResults
Detailed Results outputs a table with ninety rows, 10 to 99, with the following columns:
- *First Two Digits* The digits, 10-99
- *Rows* the number of rows in the data that start with each pair
- *Benford* the proportion of rows that would start with this pair if the data conformed to Benford's Law
- *Actual* the actual proportion of rows that start with this pair
- *Difference* the signed difference between the two, in case you want to look for over/under runs
- *Absolute Difference* the absolute difference between the two.

## How they work
All the functions follow the method described by Mark Nigrini in the companion video to his 2022 Journal of Accountancy article and demonstrated in his accompanying spreadsheet.
Article Reference: Journal of Accountancy Sept 2022 pp 12-19
Video: [Using Benford's Law to reveal journal entry anomalies](www.youtube.com/watch?v=0gxAsvLeflk) (see the video description for a demonstration spreadsheet).

These steps are: 
0. Take each of the ninety two-digit numbers from 10 to 99.
0. Count the number of numeric, non-zero rows starting with these two digits, discarding leading zeros and decimal points so that 0.01 is the same as 10.
0. Divide by the total number of numeric, non-zero rows in the column.
0. Calculate the absolute difference between this and LOG ( 1 + 1 / the two-digit number )
0. For "Average Deviation", output the average absolute difference over the ninety rows.
For detailed information on the appropriate usage of Benford's Law, a book is available:
*Benford's Law: Applications for Forensic Accounting, Auditing, and Fraud Detection*, Mark J. Nigrini, 2012, Wiley, ISBN 978-1-118-28226-7

## Usage
Use AverageDeviation to create a measure that returns the value. 
```DAX
	My Column Conformance = EJDurrant.Benford.AverageDeviation('My Table'[My Column of Numbers])
```
You can use dynamic formatting to give it a friendly form according to the standard that applies in your situation. For example, using the thresholds and labels Mark Nigrini gives in his demonstration, the following dynamic format will output the value to 4 decimal places with a judgement:
```DAX
	SWITCH (
                TRUE,
                SELECTEDMEASURE() < 0.0012, "0.#### Close conformity",
                SELECTEDMEASURE() < 0.0018, "0.#### Acceptable conformity",
                SELECTEDMEASURE() < 0.0022, "0.#### Marginally acceptable conformity",
                "0.#### Nonconformity"
            )
```
Use DetailedResults to generate a table which you can use to generate a graph. 
``` DAX
	Table of Conformance Results = EJDurrant.Benford.DetailedResults('My Table'[My Column of Numbers])
```
To use the results, you may for example:
- select the *Line and clustered column chart* visual
- place *First Two Digits* on the x-axis
- place *Benford* on the y-axis as a Line
- place *Actual* on the y-axis as a Clustered Column
This results in a well-behaved chart. You may of course also add your own columns for further analysis.

## Limitations and warnings
- The column must be considered numeric by PowerBI, or an error will occur. 
- Small numbers of non-numeric values will be discarded if PowerBI sees the column as numeric overall. To be safe, clean your data.
- Numbers with absolute values less than 0.0001 will give incorrect results. This is because we multiply by ten thousand to get rid of leading zeros and the decimal point.
- Numbers larger than one ten-thousandth of the maximum supported size will generate an error.
As always, bear in mind that only most financial data, and some other naturally-occuring data, usually conforms to Benford's Law. It does not apply to data where only a small range of numbers are possible, and there are many sets of numbers that cannot possibly, and should not, conform to Benford's Law. Study before drawing conclusions.

## Power Pivot
The Average Deviation function can be adapted to work in PowerPivot, which will almost always be more useful than Power BI in an accounting context. You will have to change the line that generates the initial list of numbers, by removing `GENERATESERIES ( 10, 99, 1 ), "First Two Digits", [Value]` and replacing it with `CALENDAR ( 10, 99 ), "@Two Digits", INT ( [Date] )`. This does exactly the same thing, and works in PowerPivot. The Detailed Results function will also work, with the same adaptation, in DAX Studio pointed at a PowerPivot model. However, since it is extremely inconvenient to create a calculated table in PowerPivot (even if technically possible in a very hacky way), you will probably do it in Power Query, which is much easier.

## About
You can find me on LinkedIn at https://www.linkedin.com/in/eleanordurrant/ and I would be interested to know if you found this useful.

## License
This project is licensed under the MIT License.
