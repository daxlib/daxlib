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

<img width="430" height="73" alt="image" src="https://raw.githubusercontent.com/avatorl/dax-udf-svg-ibcs/refs/heads/main/docs/images/516520988-15b51c49-7ba1-4a28-b33b-288b1ac536ef.png" /></br>

➡️ Then add the measure to the visual (e.g., as a table column or matrix value) or use the Image section of the Format pane (e.g., card (new), button slicer).

<img width="162" height="164" alt="image" src="https://raw.githubusercontent.com/avatorl/dax-udf-svg-ibcs/refs/heads/main/docs/images/517321532-cb14cca8-f88a-41d1-86a2-097d580fec81.png" /></br>

## Usage Examples

PBIX file: https://github.com/avatorl/PowerBI-IBCS/blob/main/UDF/DAX%20UDF%20SVG%20IBCS%20Examples.pbix

### PowerofBI.IBCS.BarChart.AbsoluteValues

<img width="267" height="150" alt="image" src="https://raw.githubusercontent.com/avatorl/dax-udf-svg-ibcs/refs/heads/main/docs/images/489960544-75cc6271-17eb-4466-bbeb-91c0641c090d.png" /></br>

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

<img width="378" height="161" alt="image" src="https://raw.githubusercontent.com/avatorl/dax-udf-svg-ibcs/refs/heads/main/docs/images/489956771-8cf32b8d-bc85-48b2-bab9-93b9b09f99c5.png" /></br>

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

<img width="290" height="150" alt="image" src="https://raw.githubusercontent.com/avatorl/dax-udf-svg-ibcs/refs/heads/main/docs/images/489965592-c8a310e4-38d4-406d-8029-4f89bba522ad.png" /></br>

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

<img width="1125" height="510" alt="image" src="https://raw.githubusercontent.com/avatorl/dax-udf-svg-ibcs/refs/heads/main/docs/images/492365981-62292dfc-ce21-4daa-9bc1-1fa3ed8afab4.png" /></br>

```
PowerofBI.IBCS.ColumnChart.WithAbsoluteVariance ( 
    [MSales AC], 
    [MSales PY], 
    [MSales PL], 
    [MSales FC], 
    'Month'[Month]
)
```

## License

This project is licensed under the MIT License.
