# DataTides.ColourContrast

Colour accessibility DAX functions for evaluating whether a foreground/background colour pair is readable. Covers WCAG relative luminance, a lightweight sRGB colour-distance metric, and the newer APCA (Advanced Perceptual Contrast Algorithm) Lc score, all working directly from HEX colour strings.

## Usage

```dax
TextLuminance =
DataTides.ColourContrast.HexLuminance(
    "#1A1A1A"   // HexCode
)

PaletteDistance =
DataTides.ColourContrast.ColorDistance2(
    "#FF5733",   // Hex1
    "#33FF57"    // Hex2
)

TextContrastLc =
DataTides.ColourContrast.APCA_Lc(
    "#1A1A1A",   // ForeHex
    "#FFFFFF"    // BackHex
)
```

## Functions

- **HexLuminance** - WCAG relative luminance (0..1) for a HEX colour string, accepting HEX with or without a leading "#"
- **ColorDistance2** - squared Euclidean distance between two HEX colours in sRGB space, useful for quick "closest colour" comparisons
- **APCA_Lc** - the APCA Lc contrast value for a foreground/background HEX pair; positive means dark text on a light background. APCA is a modern perceptual contrast method that differs from WCAG 2.x and is not yet an official standard, but is widely used for improved readability assessment

## Documentation

- See the `manifest.daxlib` and `lib/functions.tmdl` files for full parameter and return details.


## License

This project is licensed under the MIT License.
