# MartynBooth.Flags

Inline SVG country flags for Power BI and DAX.

`MartynBooth.Flags` provides a DAX user-defined function that returns country and territory flags as inline SVG data URIs. It is designed for use in Power BI reports where you want lightweight, portable flag icons without relying on external image URLs.

The function supports ISO2 codes, ISO3 codes, country names, common synonyms, UK country names, multiple display shapes, and optional SVG borders.

## Function

```dax
MartynBooth.Flags.FlagSVG(
    CountryCode,
    FlagShape,
    BorderWidth,
    BorderColour
)
```

## Why use this package?

Power BI reports often need country flags for tables, matrices, cards, tooltips, slicers, and ranking visuals. Using external image URLs can introduce dependency, privacy, security, and availability concerns.

This package keeps the flag rendering inside the model by returning a self-contained SVG data URI.

That means:

- No external image hosting required
- No web requests to retrieve flag images
- Works well in tables, matrices, cards, and custom visual layouts
- Supports common country inputs, not just strict ISO codes
- Includes multiple display shapes for different report styles
- Allows optional borders for improved contrast on light or dark report backgrounds

## Parameters

| Parameter | Type | Description |
| --- | --- | --- |
| `CountryCode` | String | ISO2 code, ISO3 code, country name, or supported synonym. Examples: `GB`, `GBR`, `United Kingdom`, `Wales`, `Scotland`, `England`. |
| `FlagShape` | String | The shape used to display the flag. |
| `BorderWidth` | Integer | Border width in SVG units. Use `0` for no border. Values between `1` and `10` usually work well. |
| `BorderColour` | String | Border colour as a hex value or SVG colour name. Examples: `#000000`, `#D0D5DD`, `black`, `white`. |

## Supported shapes

The following values are supported for `FlagShape`:

| Shape | Description |
| --- | --- |
| `rectangle` | Standard rectangular flag. |
| `rounded` | Rectangular flag with rounded corners. |
| `wavy` | Wavy flag effect with subtle shading. |
| `circle` | Circular flag icon. |
| `square` | Square flag icon. |
| `roundedsquare` | Square flag icon with rounded corners. |
| `hexagon` | Hexagonal flag icon. |
| `shield` | Shield-shaped flag icon. |
| `pin` | Map-pin style flag icon. |
| `badge` | Badge-shaped flag icon. |
| `ribbon` | Ribbon-shaped flag icon. |
| `heart` | Heart-shaped flag icon. |


![Examples of supported flag shapes](https://github.com/martynbooth/daxlib-svg-flags-assets/blob/main/FlagShapeExamples.png?raw=true)

If an unsupported shape is supplied, the function returns the `rectangle` shape.

## Supported country input

The function accepts several input formats.

### ISO2 codes

```dax
MartynBooth.Flags.FlagSVG("FR", "rounded", 2, "#D0D5DD")
```

### ISO3 codes

```dax
MartynBooth.Flags.FlagSVG("FRA", "rounded", 2, "#D0D5DD")
```

### Country names

```dax
MartynBooth.Flags.FlagSVG("France", "rounded", 2, "#D0D5DD")
```

### Common synonyms

```dax
MartynBooth.Flags.FlagSVG("USA", "circle", 2, "#D0D5DD")
```

```dax
MartynBooth.Flags.FlagSVG("United States of America", "circle", 2, "#D0D5DD")
```

### UK and home country support

The package includes support for the United Kingdom and UK country names:

```dax
MartynBooth.Flags.FlagSVG("United Kingdom", "wavy", 2, "#000000")
```

```dax
MartynBooth.Flags.FlagSVG("Wales", "wavy", 2, "#000000")
```

```dax
MartynBooth.Flags.FlagSVG("Scotland", "shield", 2, "#000000")
```

```dax
MartynBooth.Flags.FlagSVG("England", "badge", 2, "#000000")
```

The following UK-related inputs are also supported:

| Input | Returned flag |
| --- | --- |
| `GB` | United Kingdom |
| `GBR` | United Kingdom |
| `UK` | United Kingdom |
| `U.K.` | United Kingdom |
| `United Kingdom` | United Kingdom |
| `Great Britain` | United Kingdom |
| `Britain` | United Kingdom |
| `Wales` | Wales |
| `Cymru` | Wales |
| `Scotland` | Scotland |
| `England` | England |

## Example calculated column

Create a calculated column that returns a flag image for each country in your data.

```dax
Country Flag =
MartynBooth.Flags.FlagSVG(
    'Customer'[Country],
    "rounded",
    2,
    "#D0D5DD"
)
```

Then set the column's data category to **Image URL** in Power BI.

## Example measure

```dax
Selected Country Flag =
MartynBooth.Flags.FlagSVG(
    SELECTEDVALUE('Customer'[Country], "GB"),
    "circle",
    2,
    "#D0D5DD"
)
```

## Example with no border

```dax
Flag No Border =
MartynBooth.Flags.FlagSVG(
    "Japan",
    "circle",
    0,
    ""
)
```

## Example with a dark border

```dax
Flag With Dark Border =
MartynBooth.Flags.FlagSVG(
    "Wales",
    "wavy",
    3,
    "#000000"
)
```

## Example with a white border

A white border can work well on dark report backgrounds.

```dax
Flag With White Border =
MartynBooth.Flags.FlagSVG(
    "Brazil",
    "hexagon",
    4,
    "white"
)
```

## Fallback behaviour

If the country input cannot be matched to a supported flag, the function returns a fallback SVG with a question mark.

This makes missing or unexpected country values easy to spot during report development.

Examples that would return the fallback icon:

```dax
MartynBooth.Flags.FlagSVG("Unknown Country", "rounded", 2, "#D0D5DD")
```

```dax
MartynBooth.Flags.FlagSVG("", "circle", 2, "#D0D5DD")
```

## Design notes

The function returns the SVG as a UTF-8 data URI:

```text
data:image/svg+xml;utf8,...
```

The SVG is encoded inside the DAX function, so the output can be used directly in Power BI image fields without needing separate image files or external hosting.

Several shapes use clipping paths to display the flag inside a custom icon shape. The `wavy` shape also adds a subtle overlay to create a more decorative flag effect.

## Suggested usage in Power BI

This package works particularly well for:

- Country columns in tables and matrices
- Ranking tables
- Sales by country reports
- International customer dashboards
- Travel, logistics, sports, and geography reports
- Slicer-friendly country icons
- Decorative report headers or tooltip pages

For best results:

- Use `rounded`, `circle`, or `square` for compact table icons
- Use `wavy`, `shield`, `badge`, or `ribbon` for more decorative report elements
- Use a light grey border such as `#D0D5DD` on white backgrounds
- Use a white border on dark backgrounds
- Use `0` border width when the surrounding visual already provides enough contrast

## Known limitations

- The function returns a static SVG data URI. It does not fetch or update flags from an external source.
- Flag rendering depends on Power BI's support for SVG data URIs in image fields.
- Highly detailed flags may appear simplified or less legible at very small sizes.
- Some country and territory names have multiple real-world naming conventions. The package includes many common names and synonyms, but not every possible spelling or local-language variant.
- Unsupported inputs return the fallback question mark icon rather than an error.

## Package details

| Property | Value |
| --- | --- |
| Package ID | `MartynBooth.Flags` |
| Version | `1.0.0` |
| Function | `MartynBooth.Flags.FlagSVG` |
| Output | SVG data URI string |

## Example gallery

Try these examples to see the range of supported styles:

```dax
MartynBooth.Flags.FlagSVG("Wales", "wavy", 3, "#000000")
```

```dax
MartynBooth.Flags.FlagSVG("Japan", "circle", 2, "#D0D5DD")
```

```dax
MartynBooth.Flags.FlagSVG("Canada", "shield", 3, "#D0D5DD")
```

```dax
MartynBooth.Flags.FlagSVG("Brazil", "hexagon", 3, "#D0D5DD")
```

```dax
MartynBooth.Flags.FlagSVG("United Kingdom", "badge", 3, "#D0D5DD")
```

```dax
MartynBooth.Flags.FlagSVG("France", "ribbon", 3, "#D0D5DD")
```

```dax
MartynBooth.Flags.FlagSVG("Italy", "heart", 3, "#D0D5DD")
```

## Author

Created by Martyn Booth.

Built for Power BI report developers who want simple, reusable, self-contained SVG flag icons in DAX.

## Credits

The underlying flag SVG artwork is based on the excellent open-source [flag-icons](https://github.com/lipis/flag-icons) project by Panayiotis Lipiridis and contributors.

`flag-icons` is released under the MIT Licence and provides a curated collection of country flags in SVG format.

This package adapts the SVG flag artwork for use inside a DAX user-defined function, returning inline SVG data URIs for Power BI. Additional work in this package includes DAX-based country matching, ISO2/ISO3/name/synonym support, UK country support, custom display shapes, borders, fallback handling, and Power BI-specific output formatting.

The `flag-icons` project also credits the earlier SVG flag collection by `koppi`.
