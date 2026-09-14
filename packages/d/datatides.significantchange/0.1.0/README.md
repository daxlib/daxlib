# DataTides.SignificantChange

Statistical significance and outlier detection DAX functions for flagging when a value (e.g. a year-on-year change) is unusual relative to a comparison set. Covers five common tests (Z-Score, Grubbs' test, IQR, Modified Z-Score, Gaussian) plus generic helpers for surfacing which periods were flagged and their values, so you don't have to repeat the same FILTER(ALL(...)) pattern for each test.

## Usage

```dax
SpikeZScore =
DataTides.SignificantChange.ZScore(
    [YoY Change],             // _X
    [YoY Change Mean],        // _Mean
    [YoY Change StdDev]       // _StdDev
)

IsSpikeGrubbsOutlier =
DataTides.SignificantChange.IsGrubbsOutlier(
    [YoY Change],              // _X
    [YoY Change Mean],         // _Mean
    [YoY Change StdDev],       // _StdDev
    [Comparison Set Count],    // _N
    0.05                       // _Alpha
)

IsSpikeIQROutlier =
DataTides.SignificantChange.IsIQROutlier(
    [YoY Change],   // _X
    [Q1],           // _Q1
    [Q3],           // _Q3
    1.5             // _Multiplier
)

IsSpikeGaussianOutlier =
DataTides.SignificantChange.IsGaussianOutlier(
    [YoY Change],             // _X
    [YoY Change Mean],        // _Mean
    [YoY Change StdDev],      // _StdDev
    0.05                      // _Alpha
)

FlaggedYears =
DataTides.SignificantChange.FlaggedPeriods(
    DimYear[Year],              // _PeriodColumn
    [Is Spike Grubbs Outlier]   // _FlagMeasure
)
```

## Functions

- **ZScore** — how many standard deviations a value sits from the mean; |z| > 3 is a common rule of thumb
- **ModifiedZScore** — a median/MAD-based z-score, less sensitive to the outlier itself; |M| > 3.5 is a common threshold
- **NormalPDF** — the height of the normal (bell curve) density at a given point
- **NormalTailProbability** — the two-tailed probability of a value at least this extreme occurring by chance
- **IsGaussianOutlier** — full Gaussian significance test built on NormalTailProbability
- **GrubbsStatistic** — the Grubbs' G statistic: absolute distance from the mean, in standard deviations
- **GrubbsCriticalValue** — the critical G value for Grubbs' test at a given sample size and significance level
- **IsGrubbsOutlier** — full Grubbs' test built on GrubbsStatistic and GrubbsCriticalValue
- **IsIQROutlier** — flags whether a value falls outside the IQR-based outlier bounds
- **FlaggedPeriods** — concatenates every period where a boolean flag measure evaluates TRUE, for the "which years were flagged" card/table pattern
- **FlaggedValue** — surfaces a value measure's result only where a flag measure is TRUE (bare measure references only — see the function's doc comment for the MEASUREREF limitation)

All tests except FlaggedPeriods/FlaggedValue return BLANK() when `_X` is blank (e.g. the first period in a series with no prior period to compare against), rather than treating a blank as zero.

## Documentation

- See the `manifest.daxlib` and `lib/functions.tmdl` files for full parameter and return details.


## License

This project is licensed under the MIT License.
