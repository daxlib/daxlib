# SavoryData.Selection2List

A package containing DAX functions which returns a string, listing the values of a column of the filter context. These functions come in various tastes.

- Text2List: This function works with columns of any datatype and shows the values in a default format. This function comes in variants of different parameters to adjust the behavior as needd. The concrete result depends on the number of values in the filter context:
-- If one value is selected, the single value is returned. E. g. "Product A"
-- If two values are selected, both are returned with a "&" in-between. E. g. "Product A & Product B"
-- If three values are selected, then they are listed as a comma-separated list. E. g. "Product A, Product B, Product C"
-- If four or more values are selected, then only the first three are shown as a comma-separated list, with ", ..." at the end. E. g. "Product A, Product B, Product C, ..."
-- If no filter was applied, "All" is returned.


- Date2List: Tis function intended for the use of Year/Month/Date combinations. On top of the of the behavior of Text2List this function discovers if full months or years are selected (and will then not display the dates, but the months or years accordingly). If a range of dates, months or years ist selected, only the first and the last are returned, with a "-" in-between. This function comes with a variant for Year/Week/Date combinations.
-- If a full year is selected, then the full year(s) are shown. E. g. "2026" or "2025 & 2026" or "2024 - 2026"
-- If a full month is selected, then the full month(s) are shown. E. g. "January" or "January & February" or "January - March" 
-- If dates are selected, then they are listed like in the following examples. E. g. "2026-01-01" or "2026-01-01 & 2026-01-02" or "2026-01-01 - 2026" 

- MinMaxCount and MinMaxCountDate: These functions only return the first/minimal value, the last/maximal value and the count of values. This function is especially handy when debugging time intelligence logic. The difference between the two functions is that MinMaxCount shows the values in the standard format while MinMaxCountDate formats the values as "YYYY-MM-DD". E. g. "2026-01-01 - 2026-01-31 (31)".


## Usage

Find a PBIX demonstrating the use cases here:
https://sqlederhose-my.sharepoint.com/:u:/g/personal/markus_ehrenmueller-jensen_savorydata_com/EX6CKUrfCkJDrgkB04yAqu4BVWXhhK7g2QLX4AT5fz29mA?e=dVfCHC

## Documentation

- See the `manifest.daxlib` and `lib/functions.tmdl` files for more details.

## Contributing

Contributions are welcome! Please open issues or submit pull requests on GitHub.

## License

This project is licensed under the MIT License.