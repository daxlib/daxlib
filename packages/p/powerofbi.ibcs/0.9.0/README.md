# About PowerofBI.IBCS

A package of DAX user-defined functions (UDFs) for creating IBCS-guided data visualizations embedded in core Power BI visuals (matrix, button slicer, and others).

The International Business Communication Standards (IBCS) are practical proposals for the conceptual and visual design of comprehensible reports and presentations. Visit https://www.ibcs.com/ to get official information about the standards. The solution provided here is not produced, certified, authorized, or endorsed by IBCS®. The author is IBCS®-certified analyst, but not affiliated with IBCS®.

Visit https://powerofbi.org/ibcs/ to read more about this package and see interactive data visualizations created using the package.

Author: [Andrzej Leszkiewicz](https://www.linkedin.com/in/avatorl/)

## Usage Instructions

These UDFs generate charts (as SVG images) that can be embedded in core Power BI visuals such as table, matrix, button slicer, list slicer, card (new), or image (depending on the chart type).

PBIX files with examples: https://github.com/avatorl/dax-udf-svg-ibcs/tree/main/docs/pbix

➡️ Create a measure that calls one of the functions and passes all required parameters.

➡️ Change the measure's data category to "Image URL" to ensure Power BI renders the SVG images.

<img width="430" height="73" alt="image" src="https://raw.githubusercontent.com/avatorl/dax-udf-svg-ibcs/refs/heads/main/docs/images/screen_data_category.png" /><br>

➡️ Then add the measure to the visual (e.g., as a table column or matrix value)

<img width="178" height="208" alt="image" src="https://raw.githubusercontent.com/avatorl/dax-udf-svg-ibcs/refs/heads/main/docs/images/screen_visual_values.png" />

or use the Image section of the Format pane (e.g., button slicer).

<img width="162" height="164" alt="image" src="https://raw.githubusercontent.com/avatorl/dax-udf-svg-ibcs/refs/heads/main/docs/images/screen_format_image.png" /><br>

➡️ Adjust the image height and width settings in the Format pane (tables, matrix).

<img width="173" height="204" alt="image" src="https://raw.githubusercontent.com/avatorl/dax-udf-svg-ibcs/refs/heads/main/docs/images/screen_format_image_size.png" /><br>

## Functions included in the package

The following images are just screenshots. Interactive Power BI visuals created with this package and embedded in a web page using Power BI Embedded are available at https://powerofbi.org/ibcs/.

### PowerofBI.IBCS.BarChart.AbsoluteValues

Bar chart to compare absolute values (e.g., actual year vs. previous year).

<img alt="image" src="https://raw.githubusercontent.com/avatorl/dax-udf-svg-ibcs/refs/heads/main/docs/images/img-bar-chart-abs-values.png" /><br>

Recommended to use in Table, Matrix, Button Slicer visuals.

### PowerofBI.IBCS.BarChart.AbsoluteVariance

Bar chart to compare absolute variance (e.g., actual year over previous year).

<img alt="image" src="https://raw.githubusercontent.com/avatorl/dax-udf-svg-ibcs/refs/heads/main/docs/images/img-bar-chart-abs-variance.png" /><br>

Recommended to use in Table, Matrix visuals.

### PowerofBI.IBCS.BarChart.RelativeVariance

Pin chart to compare relative variance (e.g., actual year over previous year, in percent of the previous year).

<img alt="image" src="https://raw.githubusercontent.com/avatorl/dax-udf-svg-ibcs/refs/heads/main/docs/images/img-bar-chart-rel-variance.png" /><br>

Recommended to use in Table, Matrix visuals.

### PowerofBI.IBCS.BarChart.WithAbsoluteVariance

Bar chart to compare absolute values (e.g., actual year vs. previous year), with absolute variance bars.

<img alt="image" src="https://raw.githubusercontent.com/avatorl/dax-udf-svg-ibcs/refs/heads/main/docs/images/screen_button_slicer_bar_chart.png" /><br>

Recommended to use in Button Slicer.

### PowerofBI.IBCS.ColumnChart.WithAbsoluteVariance

Column chart to show absolute values (e.g., actual year and previous year) and absolute variance (actual year minus previous year) on a timeline (e.g., months on the X-axis).

<img alt="image" src="https://raw.githubusercontent.com/avatorl/dax-udf-svg-ibcs/refs/heads/main/docs/images/img-column-chart-with-abs-variance.png" /><br>

Recommended to use in Matrix, Button Slicer visuals.

### PowerofBI.IBCS.ColumnChart.AbsoluteVarianceWide

Column chart to show absolute values (e.g., actual year and previous year) and absolute variance (actual year minus previous year) on a timeline (e.g., months on the X-axis). Version with wide variance bars.

<img alt="image" src="https://raw.githubusercontent.com/avatorl/dax-udf-svg-ibcs/refs/heads/main/docs/images/img-column-chart-with-abs-variance-wide.png" /><br>

Recommended to use in Matrix, Button Slicer visuals.

### PowerofBI.IBCS.Extras.PieChart.PctOfTotal

Small pie charts to show the percent of the total for each table or matrix row.

<img alt="image" src="https://raw.githubusercontent.com/avatorl/dax-udf-svg-ibcs/refs/heads/main/docs/images/img-extras-pie-chart-pct-of-total.png" /><br>

Recommended to use in Table, Matrix visuals.

To strictly follow IBCS, pie charts can be disabled to output only the percent data label (in italic font style, which still requires using SVG because it is not supported natively for specific columns only). But I believe this form of pie charts deserves to exist if used together with absolute variance bars.

See Top N + Others Without Any Changes in the Data Model (using PowerofBI.IBCS): https://www.powerofbi.org/2025/12/02/top-n-others-without-any-changes-in-the-data-model/.

### PowerofBI.IBCS.ColumnChart.WithWaterfall

3-tier chart (timeline): column chart with absolute values, waterfall with relative changes, and relative variance pins.

<img alt="image" src="https://raw.githubusercontent.com/avatorl/dax-udf-svg-ibcs/refs/heads/main/docs/images/img-column-chart-with-waterfall.png" />

Recommended to use in Button Slicer visual.

### PowerofBI.IBCS.ColumnChart.SmallMultiple

Small multiple column charts (absolute and relative variance).

<img alt="image" src="https://raw.githubusercontent.com/avatorl/dax-udf-svg-ibcs/refs/heads/main/docs/images/screen_button_slicer_small_multiple_column_abs.png" />

<img alt="image" src="https://raw.githubusercontent.com/avatorl/dax-udf-svg-ibcs/refs/heads/main/docs/images/screen_button_slicer_small_multiple_column_rel.png" />

Recommended to use in Button Slicer visual.

### PowerofBI.IBCS.ColumnChart.Stacked

Stacked column chart. Features top N ranking (default 3) with an "Other" group, highlight of selected group (with placement of the highlighted group on the bottom of the stack), and value labels inside segments.

<img alt="image" src="https://raw.githubusercontent.com/avatorl/dax-udf-svg-ibcs/refs/heads/main/docs/images/screen-button-slicer-stacked-column.png" />

Recommended to use in Button Slicer visual.

## License

This project is licensed under the MIT License.
