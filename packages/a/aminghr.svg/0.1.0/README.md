# AminGhr.SVG

`AminGhr.SVG.BulletChart` turns any two measures into a compact bullet chart that renders inside a single table or matrix cell. It draws three qualitative background bands, a bar for the current value, a target line at 100% of target, a variance arrow with a percentage label, and an optional star for values that run past the top of the scale.

## Function

```dax
AminGhr.SVG.BulletChart (
    currentValueMeasure,
    targetValueMeasure,
    [hideAtLevel],
    [axisMaxPercent],
    [badPercent],
    [acceptablePercent],
    [satisfactoryPercent],
    [bandColor],
    [baselineColor],
    [targetLineColor],
    [unfavorableColor],
    [favorableColor],
    [starAtPercent],
    [starColor],
    [showStar]
)
```

Only the first two arguments are required. Everything else has a default.

## Setup

1. Add the function to your model, then create a measure that calls the function.
2. Select the measure and set **Measure tools > Data category > Image URL**.
3. Put the measure in a table or matrix.
4. In the visual's formatting pane, set **Image size > Height 20** and **Width 150**.

The chart is drawn on a 150 by 20 canvas, so those two numbers keep it pixel accurate. Other sizes work, but the chart stretches to fill whatever box you give it.

## Usage

### The two required arguments

Pass the measure you want to draw as the bar and the measure that represents 100% on the scale. Everything else falls back to its default: the axis runs to 130% of target, the bands break at 75%, 90% and 105%, and a star appears for anything past the top of the axis.

```dax
Sales vs PY - Bullet = AminGhr.SVG.BulletChart ( [Sales Amount], [Sales PY] )
```

<img width="1100" alt="Bullet chart with only the two required arguments" src="https://raw.githubusercontent.com/Amingharaei/assets/main/daxlib/aminghr.svg/0.1.0/bulletchart-default.png" />

The bar is blue when the value is at or above target and orange when it is below. The arrow and the percentage to the left of the axis show the variance against target, not the percentage of target, so a value at 90% of target reads as -10%.

### Hiding the chart on subtotal and total rows

A bullet chart on a grand total row is usually noise, because the total's variance is rarely comparable to the row-level variances beside it. The third argument takes any expression that returns `TRUE` where the chart should be suppressed.

```dax
Sales vs PY - Bullet =
AminGhr.SVG.BulletChart (
    [Sales Amount],
    [Sales PY],
    NOT ISINSCOPE ( 'Product'[Brand] )
)
```

<img width="1100" alt="Bullet chart suppressed on the total row" src="https://raw.githubusercontent.com/Amingharaei/assets/main/daxlib/aminghr.svg/0.1.0/bulletchart-hide-total.png" />

`ISINSCOPE` returns `TRUE` only while the given column is actually grouping the current row, so `NOT ISINSCOPE ( 'Product'[Brand] )` is `TRUE` exactly on the subtotal and grand total rows. Swap in whichever column your visual groups by, or combine several with `&&` for a matrix with more than one level.

### Changing the colors

Six colors are exposed. `bandColor` sets the base color of all three background bands, which are drawn in that one color at decreasing opacity, so the ramp stays consistent no matter what you pass. `unfavorableColor` and `favorableColor` set the bar, arrow and label below and at or above target. `starColor` sets the color for the star.

`baselineColor` and `targetLineColor` control the two reference marks independently: the tick at the start of the axis and the line at 100% of target. They share a default, but keeping them separate lets you push the target line forward, which is how the bullet chart was originally specified, while leaving the axis quiet.

```dax
Sales vs PY - Bullet =
AminGhr.SVG.BulletChart (
    [Sales Amount],
    [Sales PY],
    NOT ISINSCOPE ( 'Product'[Brand] ),
    ,
    ,
    ,
    ,
    "#B7C7D6",
    "#9AA5B1",
    "#1F1F1F",
    "#C0504D",
    "#4F81BD",
    ,
    "#F79646"
)
```

<img width="1100" alt="Bullet chart with custom colors" src="https://raw.githubusercontent.com/Amingharaei/assets/main/daxlib/aminghr.svg/0.1.0/bulletchart-custom-colors.png" />

The empty positions between the commas tell DAX to use the default for those parameters. Any CSS color value is accepted, so `"#4F81BD"`, `"rgb(79,129,189)"` and `"steelblue"` all work.

## Parameters

| Parameter | Type | Default | Description |
| --- | --- | --- | --- |
| `currentValueMeasure` | MeasureRef | required | The measure drawn as the bar. Only a measure reference is accepted. |
| `targetValueMeasure` | MeasureRef | required | The measure that represents 100% on the scale. Only a measure reference is accepted. |
| `hideAtLevel` | Boolean | `FALSE` | An expression that returns `TRUE` wherever the chart should be suppressed. |
| `axisMaxPercent` | Int64 | `130` | Percent of target at the right edge of the axis. Longer bars are clamped. |
| `badPercent` | Int64 | `75` | Anything below this percent of target is a bad result. |
| `acceptablePercent` | Int64 | `90` | Upper bound of the acceptable band, in percent of target. |
| `satisfactoryPercent` | Int64 | `105` | Upper bound of the satisfactory band, in percent of target. |
| `bandColor` | String | `"#C4C9CF"` | Base color of the three background bands. |
| `baselineColor` | String | `"#737373"` | Color of the vertical tick that marks the start of the axis. |
| `targetLineColor` | String | `"#737373"` | Color of the target line drawn at 100% of target. |
| `unfavorableColor` | String | `"#E4A35F"` | Bar, arrow and label color below target. |
| `favorableColor` | String | `"#78A1B7"` | Bar, arrow and label color at or above target. |
| `starAtPercent` | Int64 | `axisMaxPercent` | Percent of target above which the star is drawn. |
| `starColor` | String | `"#FFD700"` | Color of the star. |
| `showStar` | Boolean | `TRUE` | Set to `FALSE` to never draw the star. |

The three band thresholds are clamped so that `0 <= badPercent <= acceptablePercent <= satisfactoryPercent <= axisMaxPercent` regardless of what you pass, so a band can never be given a negative width.

## Sorting the column

The function writes a zero-padded sort key into an SVG `<desc>` element at the start of every string. Because every string shares the same prefix, sorting the column alphabetically in the visual sorts the rows by variance against target. No helper measure is needed.

## Returning blank

The function returns `BLANK` and draws nothing when the chart is hidden by `hideAtLevel`, when both measures are blank, or when the target is zero.

## Notes and limitations

The first two parameters are typed `MEASUREREF`, which means only a measure reference is accepted. A literal, a column, or an expression such as `SUM ( Sales[Amount] )` will be rejected at the call site. This is deliberate: it guarantees the context transition that makes the chart correct inside a table or matrix row. If you need a fixed target, define a measure that returns the constant and pass that.

Every number the function writes into the SVG markup is a whole number converted with an explicit `en-US` locale, so the chart renders identically in models whose culture uses the comma as a decimal separator.

The variance label itself is formatted with the model culture, so a German model shows `1.650 %` where an English one shows `1,650%`. That is intentional: the label is text a person reads, not markup a parser reads.

## License

MIT.
