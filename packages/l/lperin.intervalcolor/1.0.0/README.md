# Lperin.IntervalColor.LineColor

**Version:** 1.0.0


Author: [Lionel Perin](www.linkedin.com/in/lionel-perin-nagueu-7a4a1715a)

A DAXLib function designed to dynamically color KPI values in **Bar Charts** and **Line Charts** based on the selected months.

## Behavior

The function compares the current **Bounce Rate** with the selected months:

* 🟢 **Green** → highest bounce rate
* 🔴 **Red** → lowest bounce rate
* 🔵 **Blue** → other values

This makes it easy to visually highlight the best and worst KPI values directly in Power BI **Bar Charts** and **Line Charts**.

## Usage

Use the function as a **conditional formatting / color measure** for the chart.


- create your measure for example 
valueKpi =
SUM('VENTES'[Montant])

- Create the minimum nad maximum like 
if your axis is the date the make two kpi minKpi and maxKpi
minKpi =
MINX(
    ALLSELECTED('DATE'[Mois]),
    CALCULATE(valueKpi)
)
maxKpi =
MAXX(
    ALLSELECTED('DATE'[Mois]),
    CALCULATE(valueKpi)
)
- Call the function like that
```dax
Lperin.IntervalColor.LineColor(
    valueKpi,
    minKpi,
    maxKpi
)
```

The function returns a color name that can be used to dynamically format the chart:

```text
green
red
blue
```

## Dependencies

* `valueKpi` — the KPI value for the current point of the axis
* `minKpi` — the minimum of the KPI across the selected points
* `maxKpi` — the maximum of the KPI across the selected points

## Package

```text
Package ID: Lperin.IntervalColor
Version:    1.0.0
Function:   Lperin.IntervalColor.LineColor
```

## Visualization

Designed for:

* 📊 Power BI **Bar Charts**
* 📈 Power BI **Line Charts**

The color changes automatically according to the selected date context.
