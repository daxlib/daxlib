# KlausFolz.CountryFlags
A user-defined function for Power BI that returns country flags as SVGs.
Initially this function only supports ISO3 country codes. But more formats will be added in the future.


## Function: KlausFolz.CountryFlags.GetFlagWithISO3
Returns the flag image (as SVG code) for the given ISO3 country code.
### Syntax
```
KlausFolz.CountryFlags.GetFlagWithISO3(<ISO3CountryCode>)
```
### Parameters
- `<ISO3CountryCode>`: The ISO3 country code as string (e.g., "USA" for the United States, "DEU" for Germany).
- ### Returns
- A string containing the SVG code of the flag corresponding to the provided ISO3 country code.
### Example
```
Flag = KlausFolz.CountryFlags.GetFlagWithISO3("USA")
``` 
This will return the SVG code for the flag of the United States.

---

## Author

Klaus Folz:
[LinkedIn](https://www.linkedin.com/in/klausjfolz/) -
[GitHub](https://github.com/jurgenfolz) -
[Website](https://data-mechanics.com/)

---

## License

MIT License. Use at will, I hope you find it useful :)
