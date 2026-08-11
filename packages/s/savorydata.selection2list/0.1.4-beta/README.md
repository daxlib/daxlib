# SavoryData.Selection2List

A package containing DAX functions which return a string, listing the selected values of a column. 

## Text2List
Returns a comma separated list of the available values of the provided column in the current filter context.
The concrete result depends on the amount of selected values:
- If one value is selected, the single value is returned. (e. g. "A")
- If two values are selected, both are returned with a "&" in-between. (e. g. "A & B")
- If three or more values are selected, then they are listed as a comma-separated list. (e. g. "A, B, C")
- If more values are selected than the specified via the paramter *MaxNumberOfElements*, then only the first *MaxNumberOfElements* are shown as a comma-separated list, with ", ..." at the end. Thereby cutting off the rest of the values. (e. g. "A, B, C, ...")
- If no filter was applied, the content of parameter *TextColumName* is returned. (e. g. "All Products")
This function is an optimal addition to multi-select slicers to show the actually selected values (e. g. in a text box or button visual).

## Date2List
Returns a list of available values of the provided year, month and date in the current filter context.
The concrete result depends on the amount of selected values:
- If a single date or month or year is selected, the single value is returned. (e. g. "2026")
- If two dates or months or years are selected, both are returned with a "&" in-between. (e. g. "2026 & 2027")
- If three or more consecutive dates or months or years are selected, then the oldest and the newest value is returned, separated by dash ("-"). (e. g. "2026 - 2028")
- If three or more arbitrary dates or months or years are selected, then they are listed as a comma-separated list. By default a maximum of three elements is listed, the rest is cut off. You can change the return value of function *SavoryData.Selection2List.Config_MaxNumberOfElements* to apply a different default. (e. g. "2026, 2027, 2028, ...")
This function is an optimal addition to multi-select date slicers to show the actually selected values (e. g. in a text box or button visual).

## YQMD2List
This function is identical to *Date2List* but accepts a year, a **quarter**, a month, and a date is a parameter.
This function is an optimal addition to multi-select date slicers to show the actually selected values (e. g. in a text box or button visual).

## YWD2List
This function is identical to *Date2List* but accepts a year, a **week**, and a date is a parameter.
This function is an optimal addition to multi-select date slicers to show the actually selected values (e. g. in a text box or button visual).

## MinMaxCountV2
Returns a list of available values of the provided column in the current filter context.
Independent of the amount of selected values, it will always return the first value and the last value (converted to **text**) separated by a dash ("-") and the count of availble values in parenthesis.
This function comes handy for debugging purposes to visualize the available values in the filter context.

## MinMaxCountDate
This function is identical to *MinMaxCountV2* with two differences:
- You need to provide a (date) table as the first parameter.
- The content of the provided column is explicitely formatted as "YYYY-MM-DD".
This function comes handy for debugging purposes to visualize the available values in the filter context, especially for time intelligence functions.

## Usage

Find a PBIX demonstrating the use cases here:
https://sqlederhose-my.sharepoint.com/:u:/g/personal/markus_ehrenmueller-jensen_savorydata_com/EX6CKUrfCkJDrgkB04yAqu4BVWXhhK7g2QLX4AT5fz29mA?e=dVfCHC

## Documentation

- See the `manifest.daxlib` and `lib/functions.tmdl` files for more details.

## Contributing

Contributions are welcome! Please open issues or submit pull requests on GitHub.

## License

This project is licensed under the MIT License.