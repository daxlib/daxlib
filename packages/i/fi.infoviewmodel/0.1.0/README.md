# Fi.InfoViewModel

Returns a table using INFO.VIEW functions for purposes of self-documentation, allowing details to be exposed to users.

## Usage

```dax
EVALUATE
{
    Fi.InfoViewModel(
        "Object Model", // Name of table to exclude from the output
        TRUE(), // Include tables
        TRUE(), // Include columns
        TRUE(), // Include measures
        FALSE(), // Include hidden objects
        FALSE() // Include objects without description
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
