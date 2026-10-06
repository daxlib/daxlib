# SavoryData.Selection2List

A package containing DAX functions which returns a string, listing the selected values of a column. The concrete result depends on the amount of selected values:
- If one value is selected, the single value is returned.
- If two values are selected, both are returned with a "&" in-between.
- If three values are selected, then they are listed as a comma-separated list.
- If four or more values are selected, then only the first three are shown as a comma-separated list, with ", ..." at the end.
- If no filter was applied, "All" is returned.

Basically there are two functions:
- Text2List: Works with columns of any type as described above.
- Date2List: Is specialized for the use of Year/Month/Date combinations. On top of the description above, the function discovers if full months or years are selected (and will then not display the dates, but the months or years accordingly). If a range of dates, months or years are selected, only the first and the last are returned, with a "-" in-between.


## Usage

Find a PBIX demonstrating the use cases here:
https://sqlederhose-my.sharepoint.com/:u:/g/personal/markus_ehrenmueller-jensen_savorydata_com/EX6CKUrfCkJDrgkB04yAqu4BVWXhhK7g2QLX4AT5fz29mA?e=dVfCHC

## Documentation

- See the `manifest.daxlib` and `lib/functions.tmdl` files for more details.

## Contributing

Contributions are welcome! Please open issues or submit pull requests on GitHub.

## License

This project is licensed under the MIT License.