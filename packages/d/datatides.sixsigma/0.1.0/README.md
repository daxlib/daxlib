# DataTides.SixSigma

Reusable Six Sigma statistical process control (SPC) functions for Power BI, written as model-agnostic DAX UDFs. Covers process capability (Cp/Cpk, Pp/Ppk), Sigma Level and DPMO, calculated control limits, and the first four Nelson Rules for control chart pattern detection — all driven by table/column parameters, never hardcoded to a specific model.

## Usage

```dax
Sigma Level =
VAR Dpmo =
    'DataTides.SixSigma.DPMO'(
        [Defect Count],   // Defects
        [Unit Count],     // Units
        1                 // OpportunitiesPerUnit
    )
RETURN
    'DataTides.SixSigma.SigmaLevel'(Dpmo, 1)   // Dpmo, IncludeShift

Cpk =
'DataTides.SixSigma.Cpk'(
    Fact_GridFrequency,        // Table
    Fact_GridFrequency[f],     // ValueColumn
    Fact_GridFrequency[dtm],   // OrderColumn
    [Current USL],             // USL
    [Current LSL]              // LSL
)

Control Limits =
'DataTides.SixSigma.ControlLimits'(
    Fact_GridFrequency,      // Table
    Fact_GridFrequency[f]    // ValueColumn
)
```

## Functions

- **NormSInv** — inverse standard normal CDF, underpins `SigmaLevel`
- **DPMO** — Defects Per Million Opportunities
- **SigmaLevel** — Sigma Level from a DPMO value, with the standard 1.5σ long-term shift
- **ProcessSigmaWithin** — short-term sigma via average moving range / d2
- **Cp / Cpk** — short-term process capability (potential / actual)
- **Pp / Ppk** — long-term process capability, based on population standard deviation
- **ControlLimits** — center line, UCL, LCL (mean ± 3σ)
- **IsNelsonRule1 – IsNelsonRule4** — the first four Nelson Rules for control chart pattern detection
- **ChartSummary** — a short anomaly-flagging subtitle for a given period

## Documentation

- See the `manifest.daxlib` and `lib/functions.tmdl` files for full parameter and return details.


## License

This project is licensed under the MIT License.