# About lmolus.TopBottomDelta

💡TopBottomDelta function will allow you to show multiple insights using a single DAX measure. 

🔥Moreover, this approach will allow you to show not only the values, but also the labels within the measure!


Author: [Lukasz Molus](linkedin.com/in/łukasz-molus-386265264)

---

## Overview

> This UDF generates the concatenated labels and values for Top and Bottom Performers, with delta value.

> The DAX code is well optimized - the first variable provides (table) results, that are computed only once and used in other parts of the function (the INDEX function was used to ensure uniqueness, it also possible to use TOPn).

> Yuu can use this function in Card Visuals, Tooltips, Tables, Subtitles (below you can see some usage examples).

---

### Parameters

*   **ColumnReference:** - ANYREF EXPR - Type column name to evaluate (it will be also be used as labels);
*   **MeasureToEvaluate:** - ANYREF EXPR - Type measure that will be evaluated against column reference;
*   **FormatTypeForDeltaValue:** - STRING VAL - In what format results for delta sholud be displayed eg. "0.0%", "#,##0,.0K", etc.

ℹ️ You can also change the culture of format in the funcrion code.

## Usage example (Contoso 10M Model)

```dax
Measure = lmolus.TopBottomDelta ( 
    'Product'[Brand],          -- ColumnReference
    [Margin %],                -- MeasureToEvaluate
    "0.0%"                     -- FormatTypeForDeltaValue
)
```


➡️ Card Visual

<img width="700" height="834" alt="image" src="https://raw.githubusercontent.com/lukaszmolus/dax.udf/main/TopBottomDelta/Images/top_bottom_delta_card.png" /></br>

➡️ Subtitle

<img width="700" height="443" alt="image" src="https://raw.githubusercontent.com/lukaszmolus/dax.udf/main/TopBottomDelta/Images/top_bottom_delta_subtitle.png" />

➡️ Table

<img width="700" height="517" alt="image" src="https://raw.githubusercontent.com/lukaszmolus/dax.udf/main/TopBottomDelta/Images/top_bottom_delta_table.png" /></br>

---

## License

This project is licensed under the MIT License.
