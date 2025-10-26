# EvaluationContext.Colour

A comprehensive set of DAX User-Defined Functions (UDF) designed to enable easy manipulation of hex colours for Power BI.

## Summary

**Color Conversion:** Functions like `Int.ToHex`, `Hex.ToInt`, `RGB.ToHex`, and `HSL.ToHex` allow you to convert color values between different formats (integer, hexadecimal, RGB, HSL). This is useful for working with various colour sources

**Color Manipulation:** Functions like `Hex.AdjustHue`, `Hex.AdjustSaturation`, and `Hex.AdjustLuminance` enable you to modify existing colors. For example, you can create a lighter shade of a color by adjusting its luminance.

**Color Extraction:** Functions such as `Hex.Hue`, `Hex.Saturation`, `Hex.Luminance`, and `Hex.Alpha` can extract specific components of a hexadecimal color string

**Theming and Interpolation:** The `Hex.Theme` function provides access to pre-defined color palettes, while `Hex.LinearTheme` and `Hex.Interpolate` are used for creating gradients and smooth transitions between colors

**Text Readability:** The `Hex.TextColour` function is a great tool for automatically choosing a text color (black or white) that provides sufficient contrast against a given background color, improving readability