# InteractBI.TimeIntelligence

For more information about how this works to solve blanks and errors, see [interact.bi: Solving SAMEPERIODLASTYEAR blanks and errors](https://interact.bi/2025/10/solving-sameperiodlastyear-blanks-and-errors/)

## Usage

```dax
EVALUATE
{
    InteractBI.TimeIntelligence.SPLY (
        [Measure], //or calculation
        'Calendar Table', //the model's calendar table
        'Calendar Table'[Date], // a list of dates
        'Custom Calendar', // the custom calendar to use
        'Calendar Table'[Fiscal Year Period], // or any other column you wish to be a "period true" comparison
        'Calendar Table'[Fiscal Year Period Day Number] // in the event the filter context results in a part period selection, the "Day of X" you wish to use to shift your result back a year.
    )
}
```

## Documentation

- See the `lib/functions.tmdl` file for the functions included in this library.
- See the `manifest.daxlib` file for the library metadata and configuration.

## Contributing

Contributions are welcome! Please feel free to submit issues and pull requests.

## License

This project is licensed under the MIT License.
