# About PowerofBI.IBCS library

DAX user-defined functions for creating International Business Communication Standards (IBCS)-guided SVG visualizations embedded into core Power BI visuals.

About IBCS and IBCS-guided SVG visualizations embedded into core Power BI visuals:

🔗 https://www.powerofbi.org/ibcs/

🔗 https://www.powerofbi.org/category/data-visualization/ibcs/

Author: Andrzej Leszkiewicz

🔗 https://powerofbi.org/about

## Usage Examples

Interactive Power BI visuals embedded into a web page: https://www.powerofbi.org/powerofbi-ibcs-user-defined-functions/

PBIX files: https://github.com/avatorl/dax-udf-svg-ibcs/tree/main/docs/pbix

## Usage Instructions

These UDFs generate charts (as SVG images) that can be embedded into Power BI core visuals such as table, matrix, button slicer, list slicer, card (new), or image (depending on the chart type).

➡️ Create a measure that calls one of the functions and passes all required parameters.

➡️ Change the measure's data category to "Image URL" to ensure Power BI renders the SVG images.

<img width="430" height="73" alt="image" src="https://raw.githubusercontent.com/avatorl/dax-udf-svg-ibcs/refs/heads/main/docs/images/screen_data_category.png" /></br>

➡️ Then add the measure to the visual (e.g., as a table column or matrix value) or use the Image section of the Format pane (e.g., card (new), button slicer).

<img width="162" height="164" alt="image" src="https://raw.githubusercontent.com/avatorl/dax-udf-svg-ibcs/refs/heads/main/docs/images/screen_format_image.png" /></br>

### PowerofBI.IBCS.BarChart.AbsoluteValues

Bar chart to compare absolute values (e.g., actual year vs. previous year).

<img width="267" height="150" alt="image" src="https://raw.githubusercontent.com/avatorl/dax-udf-svg-ibcs/refs/heads/main/docs/images/screen_absolute_values.png" /></br>

Recommended to use in Table, Matrix, Button Slicer visuals.

### PowerofBI.IBCS.BarChart.AbsoluteVariance

Bar chart to compare absolute variance (e.g., actual year over previous year).

<img width="378" height="161" alt="image" src="https://raw.githubusercontent.com/avatorl/dax-udf-svg-ibcs/refs/heads/main/docs/images/screen_absolute_variance.png" /></br>

Recommended to use in Table, Matrix visuals.

### PowerofBI.IBCS.BarChart.RelativeVariance

Pin chart to compare relative variance (e.g., actual year over previous year, in percent of the previous year).

<img width="290" height="150" alt="image" src="https://raw.githubusercontent.com/avatorl/dax-udf-svg-ibcs/refs/heads/main/docs/images/screen_relative_variance.png" /></br>

Recommended to use in Table, Matrix visuals.

### PowerofBI.IBCS.ColumnChart.WithAbsoluteVariance

Column chart to show absolute values (e.g., actual year and previous year) and absolute variance (actual year minus previous year) on a timeline (e.g., months on the X-axis).

<img width="675" alt="image" src="https://raw.githubusercontent.com/avatorl/dax-udf-svg-ibcs/refs/heads/main/docs/images/screen_column_chart_absolute.png" /></br>

Recommended to use in Matrix, Button Slicer visuals.

### PowerofBI.IBCS.Extras.PieChart.PctOfTotal

Small pie charts to show the percent of the total for each table or matrix row.

To strictly follow IBCS, pie charts can be disabled to output only the percent data label (in italic font style, which still requires using SVG because it is not supported natively for specific columns only). But I believe this form of pie charts deserves to exist if used together with absolute variance bars. See Top N + Others Without Any Changes in the Data Model (using PowerofBI.IBCS): 🔗 https://www.powerofbi.org/2025/12/02/top-n-others-without-any-changes-in-the-data-model/.

<img width="123" height="295" alt="image" src="https://raw.githubusercontent.com/avatorl/dax-udf-svg-ibcs/refs/heads/main/docs/images/screen_pct_of_total_pie.png" /></br>

Recommended to use in Table, Matrix visuals.

### PowerofBI.IBCS.ColumnChart.WithWaterfall

3-tier chart (timeline): column chart with absolute values, waterfall with relative changes, and relative variance pins.

<img width="660" height="399" alt="image" src="https://raw.githubusercontent.com/avatorl/dax-udf-svg-ibcs/refs/heads/main/docs/images/screen_column_with_waterfall.png" />

Recommended to use in Button Slicer visual.

### PowerofBI.IBCS.ColumnChart.SmallMultiple

Small multiple column charts (absolute and relative variance).

<img width="724" height="499" alt="image" src="https://raw.githubusercontent.com/avatorl/dax-udf-svg-ibcs/refs/heads/main/docs/images/screen_button_slicer_small_multiple_column_abs.png" />

<img width="717" height="497" alt="image" src="https://raw.githubusercontent.com/avatorl/dax-udf-svg-ibcs/refs/heads/main/docs/images/screen_button_slicer_small_multiple_column_rel.png" />

## License

This project is licensed under the MIT License.
