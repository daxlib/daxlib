# Panoramasoft.UI

Panoramasoft.UI is a DAX library that enables you to create dynamic, data-driven visual layouts directly within Power BI. Create templates dynamic using SVG graphics for dashboards, and custom UI visualizations.

### Why Use This Library?

- ✅ **Data-Driven Visuals** - Generate layouts dynamically from your data
- ✅ **No Custom Visuals Required** - Pure DAX, works in any Power BI environment
- ✅ **Flexible & Customizable** - Control colors, positions, sizes, and labels
- ✅ **Easy Integration** - Simple function calls with intuitive parameters

## Usage

Import the library into your Power BI file and then you can follow next steps (Displaying Your Canvas in Power BI).

## 🖼️ Displaying Your Canvas in Power BI

Follow these steps to display the WindowCanvas UDF in Power BI:

#### Step 1: Create and Configure the Measure

1. **Create a Measure**

```
canvas =

VAR _layout =
"
{1, 920, 1280, 0, 0, 25, #1e1d2f},
{2, 785, 1070, 175, 100, 20, #eff2f3},
{3, 340, 165, 188, 178, 15, #FFFFFF},
{4, 340, 165, 188, 532, 15, #FFFFFF},
{5, 162, 252, 366, 178, 15, #FFFFFF},
{6, 162, 258, 632, 178, 15, #FFFFFF},
{7, 162, 252, 366, 358, 15, #FFFFFF},
{8, 162, 258, 632, 358, 15, #FFFFFF},
{9, 340, 520, 366, 532, 15, #FFFFFF},
{10, 340, 330, 900, 178, 15, #FFFFFF},
{11, 340, 330, 900, 532, 15, #FFFFFF},
{12, 48, 292, 935, 116, 25, #FFFFFF},
{13, 40, 280, 918, 30, 15, #2e2e49}
"

RETURN
    Panoramasoft.UI.WindowCanvas(_layout,true)
```

Do not forget to set the Data category as Image URL for the measure.

#### Step 2: Add and Configure the Card Visual

1. **Insert the Visual**

   - Navigate to the **Visualizations** pane
   - Select the **Card (new)** visual icon
   - Add it to your dashboard

2. **Size the Visual**
   - Resize the Card visual to fill your desired display area
   - For full-screen backgrounds, expand it to cover the entire canvas
   - The visual will serve as the container for your dynamic layout

#### Step 3: Add Measure to Visual

Once you added a value in the Data section, locate the Image section and turn it on.
Set Image type = Image URL.

In the Image URL, click the button for Conditional Format and select your measure.

Dont forget to turn off the Values and Label for the Callout values.

#### Step 4: Final Adjustments

Recommended Settings:

Padding: 0px (top, right, bottom, left) - Eliminates borders
Image Position: Left to Text - Optimal display
Remove the Card's background color for seamless integration
Disable the Card's border for a cleaner look

## Documentation

- See the `manifest.daxlib` and `lib/functions.tmdl` files for more details.

## Contributing

Contributions are welcome!
