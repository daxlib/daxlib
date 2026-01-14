# About lmolus.SVGTargetIndicator

💡 SVGTargetInidicator - set of two functions, that will allow you to show SVG target indicators based on measure reference or static value. 

ℹ️ Use Dynamic version when your target value is computed by another measure.

ℹ️ Any measures that use this function must have the "DataCategory" property = "ImageUrl".


Author: [Lukasz Molus](https://www.linkedin.com/in/%C5%82ukasz-molus-386265264/)

---

## Overview

> This UDF's generates SVG target indicators - show the status of goal completion in a clear graphical form. 

> Functions are fully configurable. You decide on the colors and the text to be displayed (depending on whether the value is below the target or not).

> Yuu can use this functions in Card Visuals, Tables, Subtitles (below you can see some usage examples).

---

### Parameters

*   **MeasureToEvaluate:** - ANYREF EXPR - Type measure that will be evaluated against the target value;
*   **TargetValue:** - ANYREF EXPR - Type measure with target value or put the static value;
*   **IconColortWhenValueOnTarget:** - STRING VAL -  Icon color when value is on target eg. "#007c66";
*   **IconColortWhenValueBelowTarget:** - STRING VAL -  Icon color when value is below target eg. "#ee0d0b";
*   **BcgColortWhenValueOnTarget:** - STRING VAL -  Background color when value is on target eg. "#e6f4eb";
*   **BcgColortWhenValueBelowTarget:** - STRING VAL -  Background color when value is below target eg. "#f5ebe4"; 
*   **TypeTextWhenValueOnTarget:** - STRING VAL -  Text to display when value is on target eg. "On target"; 
*   **TypeTextWhenValueBelowTarget:** - STRING VAL -  Text to display when value is below target eg. "Below target";  

---

## Usage example (Contoso 10M Model)

```dax
Measure = lmolus.SVGTargetIndicatorDynamicTargetValue ( 
    [Margin %],                   -- ColumnReference
    [Margin % target],            -- MeasureWithTargetReference
    "#007c66",                    -- IconColortWhenValueOnTarget
    "#ee0d0b",                    -- IconColortWhenValueBelowTarget
    "#e6f4eb",                    -- BcgColortWhenValueOnTarget
    "#f5ebe4",                    -- BcgColortWhenValueBelowTarget
    "On target",                  -- TextWhenValueOnTarget
    "Below target"                -- TextWhenValueBelowTarget
)
```


➡️ Card Visual

<img width="700" height="373" alt="image" src="https://raw.githubusercontent.com/lukaszmolus/dax.udf/main/SVGTargetInidicator/Images/SVGTargetIndicator_card.png" /></br>


➡️ Table

<img width="700" height="331" alt="image" src="https://raw.githubusercontent.com/lukaszmolus/dax.udf/main/SVGTargetInidicator/Images/SVGTargetIndicator_table.png" /></br>

---

## License

This project is licensed under the MIT License.
