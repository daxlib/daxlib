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
| **Elements** | SVG primitives (i.e. `<Rect>`, `<Circle>`, `<line>`) |
| **Compounds** | Complex components (i.e. boxplot, violin) made from `Elements` |
| **Defs** | Define reusable SVG elements (i.e. `<linearGradient>`, `<clipPath>`, and shapes) |
| **Attributes** | Create Attributes (i.e. fill, stroke, opacity) string that can be applied to `Elements` and `Defs` |
| **Transforms** | Create Transform strings |
| **Scales** | Mapping values between scales |
| **Colors** | Themes and functions for colour manipulation |

## Documentation

See the [documentation](https://evaluationcontext.github.io/daxlib.svg/) for more details.