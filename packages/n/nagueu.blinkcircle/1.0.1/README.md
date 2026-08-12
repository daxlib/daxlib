# Nagueu.BlinkCircle

A **DAXLib** library for generating SVG-based KPI alert indicators, with an animated blinking effect for critical alerts.

## 📦 Package Information

| Property        | Value                               |
| --------------- | ----------------------------------- |
| **Package**     | `Nagueu.BlinkCircle`                |
| **Version**     | `1.0.1`                             |
| **Function**    | `Nagueu.BlinkCircle.AlertIndicator` |
| **Return Type** | `STRING`                            |
| **Technology**  | DAX + SVG                           |

## 🎯 Overview

`Nagueu.BlinkCircle.AlertIndicator` generates an SVG circle that can be used as a KPI status indicator in Power BI.

The indicator behavior depends on the color provided:

* 🔴 **Red** → the circle blinks continuously.
* 🟠🟢🔵 **Other colors** → the circle remains static.
* The circle border uses the alert color.
* The static indicator uses a 30% fill opacity.
* The blinking indicator alternates between the alert color and white.

## 🚀 Usage

### Syntax

```DAX
Nagueu.BlinkCircle.AlertIndicator(
    AlertColor
)
```

### Basic Example

```DAX
Alert Indicator =
Nagueu.BlinkCircle.AlertIndicator("#F8291E")
```

The function returns an **SVG Data URI** that can be used as an image URL in Power BI.

### Dynamic Color Example

```DAX
Alert Indicator =
VAR AlertColor =
    SWITCH(
        TRUE(),
        [KPI] < 0.80, "#F8291E",
        [KPI] < 0.95, "#FFA500",
        "#00A651"
    )
RETURN
    Nagueu.BlinkCircle.AlertIndicator(AlertColor)
```

In this example:

* `#F8291E` → blinking critical alert
* `#FFA500` → static warning indicator
* `#00A651` → static OK indicator

## 🔴 Blinking Conditions

In version `1.0.0`, the blinking animation is enabled when `AlertColor` matches one of the following values:

```text
red
#f8291e
#23f8291e
```

The comparison is case-insensitive because `LOWER()` is used internally.

For example:

```DAX
Nagueu.BlinkCircle.AlertIndicator("RED")
```

will also trigger the blinking animation.

## 🎨 SVG Rendering

The generated SVG has a size of:

```text
200 × 55 px
```

The circle is positioned using:

```text
cx = 27.5
cy = 27.5
r  = 22
```

The blinking animation is implemented using CSS:

```css
@keyframes blink {
    0%   { fill: AlertColor; }
    50%  { fill: white; }
    100% { fill: AlertColor; }
}
```

The animation duration is **2 seconds** and repeats indefinitely.

## 📊 Power BI Usage

The function returns a string in the following format:

```text
data:image/svg+xml;utf8,...
```

This allows the result to be rendered as an image in Power BI.

Example:

```DAX
KPI Alert =
Nagueu.BlinkCircle.AlertIndicator(
    IF(
        [KPI Status] = "Critical",
        "#F8291E",
        "#00A651"
    )
)
```

Set the appropriate **Data category** for the measure to **Image URL** in Power BI.

## 🧩 Function Reference

### `Nagueu.BlinkCircle.AlertIndicator`

**Signature:**

```DAX
function 'Nagueu.BlinkCircle.AlertIndicator' =
(
    AlertColor: STRING
)
=>
    STRING
```

### Parameters

| Parameter    | Type     | Description                                        |
| ------------ | -------- | -------------------------------------------------- |
| `AlertColor` | `STRING` | CSS/SVG color used for the circle fill and border. |

### Return Value

```text
STRING
```

An SVG Data URI representing the KPI alert indicator.

## 💡 KPI Status Example

```DAX
KPI Status Indicator =
VAR Status =
    [KPI Status]

VAR AlertColor =
    SWITCH(
        Status,
        "Critical", "#F8291E",
        "Warning",  "#FFA500",
        "OK",       "#00A651",
        "#808080"
    )

RETURN
    Nagueu.BlinkCircle.AlertIndicator(AlertColor)
```

This provides a simple status mapping:

```text
Critical → 🔴 Blinking
Warning  → 🟠 Static
OK       → 🟢 Static
Unknown  → ⚪ Static
```

## 📌 DAXLib Metadata

```DAX
annotation DAXLIB_PackageId = Nagueu.BlinkCircle
annotation DAXLIB_PackageVersion = 1.0.0
```

## 📝 Notes

This library is designed to provide a lightweight and reusable visual component for Power BI reports using DAXLib functions.

The blinking effect relies on **CSS/SVG animation**. Rendering behavior may vary depending on the component or environment used to display the generated SVG.

---

**Nagueu.BlinkCircle — SVG KPI Alert Indicator**

Version `1.0.1`
