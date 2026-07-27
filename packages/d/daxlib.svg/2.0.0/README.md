# DaxLib.SVG

DaxLib.SVG is a DAX User-Defined Functions (UDF) library designed to make creating SVG visuals in Power BI easier.

## Getting Started

1. Download the library from [DAX Lib](https://daxlib.org/package/DaxLib.SVG/)
2. Install using TMDL view
3. Start using the functions in your measures

## Examples

For detailed examples, check out the [PBIP file](https://github.com/daxlib/dev-daxlib-svg/tree/main/samples/pbip/svg):

1. Download the source code: [Download ZIP](https://github.com/daxlib/dev-daxlib-svg/archive/refs/heads/main.zip)
2. Extract the ZIP file
3. Open `samples/pbip/svg/SVG.pbip` in Power BI Desktop
   
## Function Categories

| Category | Description |
|---|---|
| **SVG Wrapper** | Wraps one or more `Elements`, `Defs`, `Compounds`, applying required metadata and `<SVG>` tags |
| **Scales** | Mapping values between scales (`Normalize`, `NiceNum`, `NiceRange`) |
| **Data** | Shared data helpers for building axis/value tables and computing ranges (`AxisMeasure`, `Range`) |
| **Axes** | Axis layout, rendering, point mapping, and baseline computation (`MaxTickLabelWidth`, `Layout`, `Render`, `Point`, `Baseline`) |
| **Attributes** | Create attribute strings for shapes, text, and strokes (`Shapes`, `Txt`, `Stroke`) |
| **Transforms** | Create SVG transform strings (translate, rotate, scale, skew) |
| **Defs** | Define reusable SVG elements: gradients, clip paths, and shapes (`GradientStop`, `LinearGradient`, `RadialGradient`, `ClipPath`, `Circle`, `Rect`, `Line`, `Paths`) |
| **Elements** | SVG primitives: `Defs`, `UseDef`, `Group`, `Circle`, `Rect`, `Line`, `Paths`, `Ellipse`, `Polygon`, `Polyline`, `Txt` |
| **Compounds** | Complex components built from Elements: `Pill`, `Bars`, `Line`, `Area`, `Jitter`, `ProgressBar`, `Boxplot`, `Violin`, `Heatmap` |
| **Viz** | Minimal wrapper functions with defaults: `Bars`, `Line`, `Area`, `ProgressBar`, `Pill`, `Boxplot`, `Jitter`, `Heatmap`, `Violin` |
| **Colors** | Theme palettes and color manipulation: `Theme`, `PerformanceTheme`, `Int.ToHex`, `Hex.ToInt`, `RGB.ToHex`, `Hex.Interpolate` |

## Documentation

See the [documentation](https://evaluationcontext.github.io/daxlib.svg/) for more details.