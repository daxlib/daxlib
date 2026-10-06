# SavoryData.Formatting

DAX functions for creating dynamically formatted SVG text in Power BI.

`SavoryData.Formatting` is a DAX library that makes it easy to return formatted text as SVG from Power BI measures. It provides precise control over typography, alignment, colors, styles, backgrounds, positioning, and sizing.

It is especially useful for creating highly customized text and number layouts in Power BI Matrix visuals.

## Features

- Format text with custom fonts, colors, weights, and styles
- Dynamically calculate column widths
- Calculate widths for formatted numbers
- Designed for Power BI Matrix visuals
- Return SVG directly from DAX measures
- Support indentation, positioning, and custom dimensions
- Support Previous / Actual / Forecast color conventions
- Automatically calculate text width when possible
- Add lines above and below text
- Control text alignment independently for text and numbers
- Customize SVG background and line colors

## Functions

The library provides the following functions:

- `SavoryData.Formatting.FormatText`
- `SavoryData.Formatting.Width`
- `SavoryData.Formatting.WidthText`
- `SavoryData.Formatting.WidthMeasure`

## FormatText

### `SavoryData.Formatting.FormatText`

Returns an SVG containing the formatted text passed into the function.

```DAX
SavoryData.Formatting.FormatText(
    pText,
    pFontFamily,
    pFontColor,
    pFontStyle,
    pFontWeight,
    pWidth,
    pHeight,
    pIndentation,
    pY,
    pFontSize,
    pLineAbove,
    pLineBelow,
    pStrokeWidth,
    pTextAnchor,
    pBackgroundColor,
    pLineColor
)
```

#### Parameters

| Parameter | Description | Default |
|---|---|---|
| `pText` | The text to be formatted. | — |
| `pFontFamily` | The font to use. Any font installed on your device can be used. | `Arial, sans-serif` |
| `pFontColor` | The color of the text. Supports RGB/hex codes, color names, and IBSC-conform color codes. | `#888888` |
| `pFontStyle` | Font style. Use `"italic"` for italic text. | — |
| `pFontWeight` | Font weight. Use `"bold"` for bold text. | — |
| `pWidth` | Width of the SVG in pixels. Leave blank to calculate the optimal width automatically. | Automatic |
| `pHeight` | Height of the SVG in pixels. | `auto` |
| `pIndentation` | Indentation in pixels. | None |
| `pY` | Y-position of the text. Can be specified in pixels or as a percentage of the height. | `80%` |
| `pFontSize` | Font size in pixels. | `18` |
| `pLineAbove` | If not empty, a line is added above the text. | None |
| `pLineBelow` | If not empty, a line is added below the text. | None |
| `pStrokeWidth` | Width of the line(s) in pixels. | `3` |
| `pTextAnchor` | Text alignment. Use `"start"` for left-aligned text and `"end"` for right-aligned numbers. | `start` |
| `pBackgroundColor` | Background color of the SVG. | `#ffffff` |
| `pLineColor` | Color of the line(s). | `#888888` |

### Font Colors

The following IBSC-conform color codes are also supported:

| Code | Description | Hex |
|---|---|---|
| `Previous` | Neutral gray for historical comparison | `#6e6e6e` |
| `Actual` | Dark color for the primary value | `#404040` |
| `Forecast` | Light gray for planned/expected values | `#b0b0b0` |

### Font Style

Set `pFontStyle` to:

```text
italic
```

to display the text in italic.

Leave the parameter blank for the default font style.

### Font Weight

Set `pFontWeight` to:

```text
bold
```

to display the text in bold.

Leave the parameter blank for the default font weight.

### Width

`pWidth` controls the width of the SVG in pixels.

If left blank, the function automatically calculates the optimal width.

You can explicitly specify a width when the automatically calculated width does not fit your requirements.

### Height

`pHeight` controls the height of the SVG in pixels.

If left blank, `auto` is used, which falls back to the height setting of the Power BI Matrix visual.

### Indentation

`pIndentation` specifies the indentation in pixels.

If left blank, no indentation is applied.

### Y Position

`pY` controls the vertical position of the text.

The position can be specified either in pixels or as a percentage of the SVG height.

The default is:

```text
80%
```

This works well for most use cases.

### Font Size

`pFontSize` controls the font size in pixels.

The default is:

```text
18
```

### Lines Above and Below

Use `pLineAbove` to add a line above the text.

Use `pLineBelow` to add a line below the text.

The line is added when the corresponding parameter is not empty.

### Stroke Width

`pStrokeWidth` controls the width of the line(s) in pixels.

The default is:

```text
3
```

### Text Anchor

`pTextAnchor` controls the horizontal alignment of the text.

For text, the default value is:

```text
start
```

This left-aligns the text.

For numbers, use:

```text
end
```

This right-aligns the value.

### Background Color

`pBackgroundColor` controls the background color of the SVG.

The default is:

```text
#ffffff
```

### Line Color

`pLineColor` controls the color of lines added above or below the text.

The default is:

```text
#888888
```

---

## Width

The `SavoryData.Formatting.Width` function calculates the width in pixels based on the given font family, font size, and an optional factor.

The result can and should be used for the width of a column in a Power BI Matrix visual.

### `SavoryData.Formatting.WidthText`

`WidthText` is a specialized version of `Width`.

It removes filters before calculating the width in order to determine the **maximum possible width of the text column**.

This is useful when the column needs to be wide enough to accommodate the longest possible text value, regardless of the current filter context.

### `SavoryData.Formatting.WidthMeasure`

`WidthMeasure` is a specialized version of `Width` designed to calculate the width of formatted numbers.

It accepts a format string and removes filters before calculating the width of the formatted number.

This is particularly useful for numeric Matrix columns.

For example, a column may contain:

```text
1,234
12,345
123,456
1,234,567
```

The required width of the column depends on the largest formatted value.

`WidthMeasure` can calculate the width required for the maximum possible formatted number.

---

## Examples

### Basic Text

A simple formatted text measure:

```DAX
Formatted Text =
SavoryData.Formatting.FormatText(
    "Revenue",
    "Arial, sans-serif",
    "#404040",
    BLANK(),
    "bold",
    BLANK(),
    BLANK(),
    BLANK(),
    "80%",
    18,
    BLANK(),
    BLANK(),
    3,
    "start",
    "#FFFFFF",
    "#888888"
)
```

### Formatted Number

For numbers, use `pTextAnchor = "end"` to right-align the value:

```DAX
Formatted Value =
SavoryData.Formatting.FormatText(
    FORMAT([Revenue], "#,##0"),
    "Arial, sans-serif",
    "Actual",
    BLANK(),
    BLANK(),
    BLANK(),
    BLANK(),
    BLANK(),
    "80%",
    18,
    BLANK(),
    BLANK(),
    3,
    "end",
    "#FFFFFF",
    "#888888"
)
```

### Bold Text

```DAX
Bold Text =
SavoryData.Formatting.FormatText(
    "Revenue",
    BLANK(),
    "Actual",
    BLANK(),
    "bold",
    BLANK(),
    BLANK(),
    BLANK(),
    "80%",
    18,
    BLANK(),
    BLANK(),
    BLANK(),
    "start",
    BLANK(),
    BLANK()
)
```

### Italic Text

```DAX
Italic Text =
SavoryData.Formatting.FormatText(
    "Forecast",
    BLANK(),
    "Forecast",
    "italic",
    BLANK(),
    BLANK(),
    BLANK(),
    BLANK(),
    "80%",
    18,
    BLANK(),
    BLANK(),
    BLANK(),
    "start",
    BLANK(),
    BLANK()
)
```

### Text with a Line Below

```DAX
Text With Line =
SavoryData.Formatting.FormatText(
    "Revenue",
    BLANK(),
    "Actual",
    BLANK(),
    "bold",
    BLANK(),
    BLANK(),
    BLANK(),
    "80%",
    18,
    BLANK(),
    "Line",
    3,
    "start",
    BLANK(),
    "#888888"
)
```

---

## Power BI Setup

To use `SavoryData.Formatting.FormatText` in a Power BI Matrix visual, a few configuration steps are required.

### 1. Set Data Category to Image URL

For measures using:

```DAX
SavoryData.Formatting.FormatText
```

set the **Data Category** of the measure to:

> **Image URL**

This tells Power BI to interpret the SVG returned by the measure as an image.

Without this setting, Power BI will not render the SVG as an image in the Matrix.

### 2. Configure Matrix Image Size

In the Power BI Matrix visual:

1. Open the **Format** pane.
2. Go to **Image size**.
3. Set **Width** to `512px`.
4. Set **Height** to approximately **2× the font size**.

For example, for an 18px font, start with:

```text
Height = 36px
```

The optimal height depends on the font family and its metrics, so you may need to adjust the value.

### 3. Configure Column Width

Create width measures using:

- `SavoryData.Formatting.WidthText` for text columns
- `SavoryData.Formatting.WidthMeasure` for formatted number columns

Then use the corresponding measure under:

> **Layout → Column width**

This allows the Matrix column width to be calculated dynamically.

---

## Recommended Matrix Configuration

The following settings are recommended as a starting point:

| Setting | Recommended value |
|---|---|
| Data Category | `Image URL` |
| Image width | `512px` |
| Image height | Approximately `2 × font size` |
| Column width | Use measures to calculate the width |

The exact image height may need to be adjusted depending on the font family and font size.

---

## Typical Usage Pattern

A common setup is to create:

1. One measure that returns the formatted SVG.
2. One measure that calculates the required column width.

For example, a text measure could look like this:

```DAX
Product Name =
SavoryData.Formatting.FormatText(
    SELECTEDVALUE('Product'[Product]),
    "Arial, sans-serif",
    "Actual",
    BLANK(),
    BLANK(),
    BLANK(),
    BLANK(),
    BLANK(),
    "80%",
    18,
    BLANK(),
    BLANK(),
    3,
    "start",
    "#FFFFFF",
    "#888888"
)
```

And a corresponding width measure:

```DAX
Product Name Width =
SavoryData.Formatting.WidthText(
    ...
)
```

Use `Product Name` as the displayed value in the Matrix and `Product Name Width` to control the column width.

---

## Example: Text and Number Columns

A Matrix can combine formatted text and numbers.

For text, use:

```DAX
Product =
SavoryData.Formatting.FormatText(
    SELECTEDVALUE('Product'[Product]),
    BLANK(),
    "Actual",
    BLANK(),
    BLANK(),
    BLANK(),
    BLANK(),
    BLANK(),
    "80%",
    18,
    BLANK(),
    BLANK(),
    3,
    "start",
    "#FFFFFF",
    "#888888"
)
```

For numbers, use:

```DAX
Revenue =
SavoryData.Formatting.FormatText(
    FORMAT([Revenue], "#,##0"),
    BLANK(),
    "Actual",
    BLANK(),
    BLANK(),
    BLANK(),
    BLANK(),
    BLANK(),
    "80%",
    18,
    BLANK(),
    BLANK(),
    3,
    "end",
    "#FFFFFF",
    "#888888"
)
```

The text is left-aligned using:

```text
start
```

while the numeric value is right-aligned using:

```text
end
```

This makes the SVG-based Matrix layout behave more like a conventional Power BI table.

---

## Example PBIX

A Power BI `.pbix` demonstrating the parameters and their effects is available here:

https://sqlederhose-my.sharepoint.com/:u:/g/personal/markus_ehrenmueller-jensen_savorydata_com/IQAHYzUQuJYDRZk4W_BbjKuiAbZiP4NFnpOGd3ANtOLxV4s?e=rLQEHc

The example demonstrates the available parameters and their effects in a Power BI Matrix visual.

---

## Documentation

For more details about the implementation and available functions, see:

- [`manifest.daxlib`](manifest.daxlib)
- [`lib/functions.tmdl`](lib/functions.tmdl)

These files contain the detailed function definitions and additional implementation information.

---

## Contributing

Contributions are welcome!

If you find a bug, have an idea for an improvement, or would like to add functionality, please open an issue or submit a pull request.

### Issues

Use GitHub Issues to:

- Report bugs
- Request new features
- Suggest improvements
- Ask questions about the library

When reporting a bug, please include:

- Power BI version, where relevant
- The DAX expression being used
- The expected behavior
- The actual behavior
- A minimal example, if possible

### Pull Requests

Pull requests are welcome.

When submitting a pull request:

1. Keep the changes focused.
2. Include documentation for new functionality.
3. Add examples where they help explain the behavior.
4. Make sure existing functionality is not unintentionally affected.

---

## License

This project is licensed under the **MIT License**.

See the [`LICENSE`](LICENSE) file for the full license text.
