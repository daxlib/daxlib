# Nagueu.KPI.MonthColor

**Version:** 1.0.0

A DAXLib function designed to dynamically color KPI values in **Bar Charts** and **Line Charts** based on the selected months.

## Behavior

The function compares the current **Bounce Rate** with the selected months:

* 🟢 **Green** → highest bounce rate
* 🔴 **Red** → lowest bounce rate
* 🔵 **Blue** → other values

This makes it easy to visually highlight the best and worst KPI values directly in Power BI **Bar Charts** and **Line Charts**.

## Usage

Use the function as a **conditional formatting / color measure** for the chart.

```dax
Month Color =
    'Nagueu.KPI.MonthColor'()
```

The function returns a color name that can be used to dynamically format the chart:

```text
green
red
blue
```

## Dependencies

* `ValueKPI`
* `'DATE'[Année]`
* `'DATE'[mois]`
* `'DATE'[Mois_num]`

## Package

```text
Package ID: Lionel.KPI
Version:    1.0.0
Function:   Lionel.KPI.MonthColor
```

## Visualization

Designed for:

* 📊 Power BI **Bar Charts**
* 📈 Power BI **Line Charts**

The color changes automatically according to the selected date context.
