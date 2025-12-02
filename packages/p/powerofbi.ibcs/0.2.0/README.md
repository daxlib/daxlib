# About PowerofBI.IBCS library

DAX user-defined functions for creating International Business Communication Standards (IBCS)-guided SVG visualizations embedded into core Power BI visuals.

About IBCS and IBCS-guided SVG visualizations embedded into core Power BI visuals:

🔗 https://www.powerofbi.org/ibcs/

🔗 https://www.powerofbi.org/category/data-visualization/ibcs/

Author: Andrzej Leszkiewicz

🔗 https://powerofbi.org/about

## Usage Instructions

These UDFs generate charts (as SVG images) that can be embedded into Power BI core visuals such as table, matrix, button slicer, list slicer, card (new), or image (depending on the chart type).

➡️ Create a measure that calls one of the functions (see function-specific instructions inside the function code).

➡️ Change the measure's data category to "Image URL" to ensure Power BI renders the SVG images.

<img width="430" height="73" alt="image" src="https://raw.githubusercontent.com/avatorl/dax-udf-svg-ibcs/refs/heads/main/docs/images/screen_data_category.png" /></br>

➡️ Then add the measure to the visual (e.g., as a table column or matrix value) or use the Image section of the Format pane (e.g., card (new), button slicer).

<img width="162" height="164" alt="image" src="https://raw.githubusercontent.com/avatorl/dax-udf-svg-ibcs/refs/heads/main/docs/images/screen_format_image.png" /></br>

## Usage Examples

PBIX file: https://github.com/avatorl/PowerBI-IBCS/blob/main/UDF/DAX%20UDF%20SVG%20IBCS%20Examples.pbix

### PowerofBI.IBCS.BarChart.AbsoluteValues

<img width="267" height="150" alt="image" src="https://raw.githubusercontent.com/avatorl/dax-udf-svg-ibcs/refs/heads/main/docs/images/screen_absolute_values.png" /></br>

Use in Table, Matrix, Button List visuals.

```
PowerofBI.IBCS.BarChart.AbsoluteValues(
    [AC],
    BLANK (),
    [PY],
    "grey",
    FORMAT ( [AC], "#0" ),
    NOT ( ISINSCOPE ( 'Sales Person'[Name] ) ),
    MAXX ( ALLSELECTED ( 'Sales Person' ), MAX ( [AC], [PY] ) )
)
```

### PowerofBI.IBCS.BarChart.AbsoluteVariance

<img width="378" height="161" alt="image" src="https://raw.githubusercontent.com/avatorl/dax-udf-svg-ibcs/refs/heads/main/docs/images/screen_absolute_variance.png" /></br>

Use in Table, Matrix, Button List visuals.

```
PowerofBI.IBCS.BarChart.AbsoluteVariance (
    [DeltaPY],
    FORMAT ( [DeltaPY], "#0" ),
    NOT ( ISINSCOPE ( 'Sales Person'[Name] ) ),
    MAXX ( ALLSELECTED ( 'Sales Person' ), [DeltaPY] ),
    MINX ( ALLSELECTED ( 'Sales Person' ), [DeltaPY] ),
    MAX (
        MAXX ( ALLSELECTED ( 'Sales Person' ), [AC] ),
        MAXX ( ALLSELECTED ( 'Sales Person' ), [PY] )
    )    
)
```

### PowerofBI.IBCS.BarChart.RelativeVariance

<img width="290" height="150" alt="image" src="https://raw.githubusercontent.com/avatorl/dax-udf-svg-ibcs/refs/heads/main/docs/images/screen_relative_variance.png" /></br>

Use in Table, Matrix, Button List visuals.

```
PowerofBI.IBCS.BarChart.RelativeVariance (
    [DeltaPY%],
    FORMAT ( [DeltaPY%], "+#,0;-#,0;#,0" ),
    NOT ( ISINSCOPE ( 'Sales Person'[Name] ) )
)
```

Use in Matrix, Button List visuals.

### PowerofBI.IBCS.ColumnChart.WithAbsoluteVariance

<img width="1125" height="510" alt="image" src="https://raw.githubusercontent.com/avatorl/dax-udf-svg-ibcs/refs/heads/main/docs/images/screen_column_chart_waterfall.png" /></br>

```
PowerofBI.IBCS.ColumnChart.WithAbsoluteVariance ( 
    [MSales AC], 
    [MSales PY], 
    [MSales PL], 
    [MSales FC], 
    'Month'[Month]
)
```

### PowerofBI.SVG.PctOfTotalwithPieChart

<img width="123" height="295" alt="image" src="https://raw.githubusercontent.com/avatorl/dax-udf-svg-ibcs/refs/heads/main/docs/images/screen_pct_of_total_pie.png" /></br>

```
VAR _Total =
    CALCULATE ( [AC], ALLSELECTED () )
VAR _ValuePct =
    DIVIDE ( [AC], _Total )
VAR _IsTotalRow =
    NOT ( ISINSCOPE ( 'Payment Mode'[Payment Mode] ) )
VAR _Result =
    PowerofBI.SVG.PctOfTotalwithPieChart (
        _ValuePct,
        FORMAT ( _ValuePct, "#,0%" ),
        _IsTotalRow,
        TRUE (),
        0,
        0
    )
RETURN
    _Result
```

## License

This project is licensed under the MIT License.
