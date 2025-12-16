# NativeHTML UDF Documentation

- [LinkedIn: Edward Charles](https://www.linkedin.com/in/edward-charles-085025b1/)
- [YouTube: @DropMaterializedView](https://www.youtube.com/@DropMaterializedView)

## Overview
The `NativeHTML` function allows you to render rich HTML and CSS within Power BI visuals (designed ideally for the image visual) by wrapping them in an SVG `foreignObject`.

### Variables
*   **Width:** - DOUBLE - Width of the SVG viewport/canvas in pixels
*   **Height:** - DOUBLE - Hieght of the SVG viewport/canvas in pixels
*   **IsVisibile:** - BOOLEAN - Toggle for visibility (TRUE renders normally, FALSE applies visibility:hidden), this allows for you to make a text box conditionally visibile. 
*   **HtmlContent:** - STRING - The raw HTML text or markup to be rendered inside the SVG
*   **CssContent:** - STRING - Optional custom CSS string (if set to "", uses DefaultCss; if populated, replaces DefaultCss)

### Returns
SVG data URI string for use in Image visual

## Usage
```dax
Measure = NativeHTML( 
    200,                -- Width
    100,                -- Height
    TRUE,               -- IsVisible
    "<b>Hello</b>",     -- HtmlContent
    "color:red;"        -- CssContent
)
```

> [!WARNING]
> * **Image not showing?** Make sure the *Data Category* is set to **Image URL**.
> * **Text cut off?** Verify that `Width` and `Height` values are large enough.
> * **Quotes breaking DAX?** Use double double-quotes `""` to escape the `"` character in your HTML DAX string.

## Compatible HTML Tags
Power BI's rendering engine (via SVG foreignObject) supports most standard HTML5 tags. However, interactivity is limited.

| Tag Category | Supported Tags | Notes |
| :--- | :--- | :--- |
| **Text** | `p`, `span`, `div`, `h1`-`h6`, `b`, `i`, `u`, `strong`, `em` | Great for rich text formatting. |
| **Lists** | `ul`, `ol`, `li` | Standard bullet/number lists work well. |
| **Tables** | `table`, `tr`, `td`, `th`, `thead`, `tbody` | Useful for mini-grids inside a cell. |
| **Media** | `img` | **Important:** `src` should be a **Data URL** (Base64). External URLs (https://...) are often blocked by CORS or Power BI security. |
| **Layout** | `div`, `section`, `br`, `hr` | `flex` and `grid` layouts in CSS work! |

### ❌ Unsupported / Blocked
*   `<script>`: Javascript is strictly blocked for security.
*   `<iframe>`: Generally blocked.
*   External resources (fonts, images) may not load depending on Power BI environment (Desktop vs Service). **Embed everything** for best results.

## Nested Layouts (Divs within Divs)
Yes! You can nest as many `<div>` tags as you like within the `HtmlContent` parameter. This is essential for complex layouts like cards, grids, or multi-column text.

```dax
NativeHTML(
    300, 150, TRUE,
    
    -- HtmlContent with nested structure
    "<div style='display:flex; gap:10px;'>" &
    "  <div style='flex:1; background: #ddd; padding: 10px;'>" &
    "    <b>Left Column</b><br/>Details here..." &
    "  </div>" &
    "  <div style='flex:1; background: #bbb; padding: 10px;'>" &
    "    <b>Right Column</b><br/>More info..." &
    "  </div>" &
    "</div>",
    
    -- Main Container CSS
    "font-family: Arial; padding: 10px;" 
)
```

## Styling with CSS
You can pass a CSS string to the `CssContent` parameter. This is applied to a wrapper `<div>` surrounding your content.

### Using the `style` Attribute
You can also use inline styles within your `HtmlContent`.

### Supported CSS Properties
Virtually all CSS properties supported by modern browsers work here, including:
*   **Layout:** `display: flex`, `display: grid`, `position: absolute`.
*   **Typography:** `font-family`, `font-size`, `color`, `text-shadow`.
*   **Decorations:** `background`, `border`, `box-shadow`, `linear-gradient`.
*   **Transforms:** `transform: rotate(...)`, `scale(...)`.

## Examples

### 1. Simple KPI Badge
```dax
NativeHTML(
    100, 30, TRUE,
    "95%",
    "background:green; color:white; border-radius:15px; text-align:center; line-height:30px; font-family:sans-serif;"
)
```

### 2. Complex Card (Nested Divs with Distinct Styles)
This example demonstrates a card with a header, body, and footer, each styled differently using nested `<div>`s.

```dax
NativeHTML(
    300, 200, TRUE,
    
    -- HtmlContent: Three nested divs for Header, Body, Footer
    "<div style='background: #333; color: white; padding: 10px; font-weight: bold; border-top-left-radius: 8px; border-top-right-radius: 8px;'>" &
    "  Sales Report" &
    "</div>" &
    
    "<div style='background: white; padding: 20px; color: #333; height: 100px;'>" &
    "  <p>Total Revenue: <b>$1.2M</b></p>" &
    "  <p style='color: gray; font-size: 12px;'>vs last year</p>" &
    "</div>" &
    
    "<div style='background: #f0f0f0; padding: 10px; text-align: right; border-bottom-left-radius: 8px; border-bottom-right-radius: 8px; color: #666; font-size: 11px;'>" &
    "  Updated: Today" &
    "</div>",
    
    -- CssContent: Main container wrapper functionality
    "font-family: 'Segoe UI', sans-serif; box-shadow: 0 4px 8px rgba(0,0,0,0.1); border-radius: 8px;"
)
```