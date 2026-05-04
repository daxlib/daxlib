# Fi.Convert.MDtoHTML

Converts a subset of basic Markdown to HTML.

The goal is to implement core features of Markdown, allowing developers to create documentation for HTML visuals without having to maintain actual HTML within the model.  
Ideally keeping to a single function and without major performance impact.

An example use case is to document basic features of a report, list of contacts and external links all in a single measure with dynamic references, such as last dates in tables, refresh timestamp, etc.

## Supported Syntax

- ✅: Fully supported
- *️⃣: Partially supported
- 🟨: Not supported, but planned
- 🟥: Not supported (may still be implemented, but it's unlikely)

| Syntax | Support | Note |
|---|:---:|---|
| Heading |  *️⃣ | Hash (#) syntax only |
| Paragraph | ✅ |  |
| Line break | ✅ |  |
| Emphasis | *️⃣ | Asterisk (*) syntax |
| Blockquote | 🟨 |  |
| Unordered list | *️⃣ | Dash (-) syntax, nested emphasis and links supported |
| Ordered list | 🟨 |  |
| Mixed list | 🟥 |  |
| Inline code | 🟨 |  |
| Code block | 🟨 |  |
| Horizontal rule | *️⃣ | Dash (-) syntax |
| Link | *️⃣ | Angled bracket and reference not supported |
| Image | 🟨 |  |
| Escaping | 🟥 |  |
| Table | 🟥 |  |
| Footnote | 🟥 |  |
| Heading ID | 🟥 |  |
| Definition | 🟥 |  |
| Strikethrough | 🟥 |  |
| Task list | 🟥 |  |
| Emoji | 🟥 |  |
| Highlight | 🟥 |  |
| Subscript | 🟥 |  |
| Superscript | 🟥 |  |

## Usage

```dax
EVALUATE
{
    Fi.Convert.MDtoHTML()
}
```

### Sample

Input:

```md
# Heading 1
## Heading 2
### Heading 3
#### Heading 4
##### Heading 5
###### Heading 6

---

*This* **section** is a ***paragraph***.

Line 1 link [Example](https://example.com)  
Line 2 link emphasis **[Example](https://example.com)**

- Unordered
    - Multi-level
- List
```

Output:

```html
<h1>Heading 1</h1>
<h2>Heading 2</h2>
<h3>Heading 3</h3>
<h4>Heading 4</h4>
<h5>Heading 5</h5>
<h6>Heading 6</h6>
<hr>
<p><em>This</em> <strong>section</strong> is a <strong><em>paragraph</em></strong>.</p>
<p>Line 1 link <a href="https://example.com">Example</a><br>Line 2 link emphasis <strong><a href="https://example.com">Example</a></strong></p>
<ul>
    <li>Unordered
    <ul>
        <li>Multi-level</li>
    </ul>
    </li>
    <li>List</li>
</ul>
```

## Documentation

- See the `lib/functions.tmdl` file for the functions included in this library.
- See the `manifest.daxlib` file for the library metadata and configuration.

## Contributing

Contributions are welcome! Please feel free to submit issues and pull requests.  
I'd like to keep to the following:  

- Single function, no helper functions
- Manageable performance impact
- Somewhat sustainable size (not thousands of lines of code)
- Use only primary syntax, where multiple are supported (e.g. asterisk for emphasis)

## License

This project is licensed under the MIT License.
