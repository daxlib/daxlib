# EG.GradientColor

Function to replicate the [native gradient conditional formatting](https://learn.microsoft.com/en-us/power-bi/create-reports/desktop-conditional-table-formatting) of Power BI through a measure, enabling full control over the color scale and dynamic palette switching. Works with both regular measures and **Visual Calculations**.

## What it does

`EG.GradientColor.Compute` returns a string that can be used in the **Background color** or **Font color** field of any Power BI visual that accepts a measure for conditional formatting.

> **Note:** All color parameters must follow the exact format `"RGBA(r,g,b,a)"` — e.g., `"RGBA(255,0,0,1)"`. Any deviation will cause the function to return an incorrect result.

---

## Function

```DAX
EG.GradientColor.Compute (
    <valueExpr>,            -- current value, e.g., [Sales Amount]
    <visualMatrix>,         -- table capturing all values in the visual (see Model requirements)
    <matrixValueColumn>,    -- e.g., [@Value] or [Sales Amount]
    <RGBAMin>,              -- e.g., "RGBA(255,0,0,1)"   -- color for the minimum value
    <RGBAMid>,              -- e.g., "RGBA(255,255,0,1)" -- color for the midpoint
    <RGBAMax>               -- e.g., "RGBA(0,255,0,1)"   -- color for the maximum value
)
```

**Return:** A string in `RGBA(r,g,b,a)` format representing the interpolated color for the current cell value, ready to be used in a conditional formatting color measure.

---

## Model requirements

### `<visualMatrix>` — the visual context table

The **matrix in which the visual is placed**, and where you want to determine the minimum and maximum values for calculating the gradient.

**In a regular measure:** use `SUMMARIZECOLUMNS` + `ALLSELECTED()` with all rows and columns of the visual, plus the measure to evaluate.

**In a Visual Calculation:** use `ROWS COLUMNS` + `EXPAND` and `COLLAPSEALL` to reference all items in the visual.

### `<matrixValueColumn>` — the values to evaluate

The column inside `<visualMatrix>` that holds the values for gradient scoring. In regular measures this is usually a virtual column created inside `SUMMARIZECOLUMNS` (e.g., `[@Value]`). In Visual Calculations it is the measure column directly (e.g., `[Sales Amount]`).

### Dynamic color palettes (optional)

To make the color palette switchable, create a disconnected table with the RGBA values and use `SELECTEDVALUE()`:

```DAX
Gradient_Color_Palettes = 
DATATABLE (
    "Color Name", STRING,
    "RGBA Min",   STRING,
    "RGBA Mid",   STRING,
    "RGBA Max",   STRING,
    {
        { "Vivid Contrast",    "RGBA(75,0,130,1)",   "RGBA(255,105,180,1)", "RGBA(0,255,255,1)"  },
        { "Extreme Diverging", "RGBA(0,0,90,1)",     "RGBA(255,255,255,1)", "RGBA(255,120,0,1)"  },
        { "Traffic Light",     "RGBA(180,0,0,1)",    "RGBA(255,200,0,1)",   "RGBA(0,150,0,1)"    }
    }
)
```

---

## Basic usage

### In a regular measure

```DAX
Gradient =
EG.GradientColor.Compute (
    [Sales Amount],
    CALCULATETABLE (
        SUMMARIZECOLUMNS (
            'Product'[Brand],
            'Date'[Year Month Short],
            "@Value", [Sales Amount]
        ),
        ALLSELECTED ()
    ),
    [@Value],
    SELECTEDVALUE ( 'Gradient_Color_Palettes'[RGBA Min] ),
    SELECTEDVALUE ( 'Gradient_Color_Palettes'[RGBA Mid] ),
    SELECTEDVALUE ( 'Gradient_Color_Palettes'[RGBA Max] )
)
```

A visual with `'Product'[Brand]` on the rows and `'Date'[Year Month Short]` on the columns

### In a Visual Calculation

```DAX
Gradient =
EG.GradientColor.Compute (
    [Sales Amount],
    CALCULATETABLE (
        CALCULATETABLE (
            ROWS COLUMNS,
            EXPAND ( ROWS COLUMNS )
        ),
        COLLAPSEALL ( ROWS COLUMNS )
    ),
    [Sales Amount],
    "RGBA(255,0,0,1)",
    "RGBA(255,255,0,1)",
    "RGBA(0,255,0,1)"
)
```

The same visual being referenced with `ROWS COLUMNS`.
