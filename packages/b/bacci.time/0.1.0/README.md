# Bacci.Time

A user-defined function that calculates the duration between two dates in the format `n years n months n days.`

Most existing algorithms I found rely on a simplified calendar (assuming 30 days per month and 365 days per year) and use basic `MOD()` and `QUOTIENT()` operations to determine the duration. This often produces inaccurate or unintuitive results.
In contrast, this UDF calculates durations using actual month and year boundaries, resulting in a more natural, human-readable output. The results are displayed side by side for easy comparison with the conventional method.

![Thumbnail](https://raw.githubusercontent.com/PBI-David/PBI-Core-Visuals-SVG-HTML/refs/heads/main/Duration%20(DAX%20UDF)/thumbnail.png)
