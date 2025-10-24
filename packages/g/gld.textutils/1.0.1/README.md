
# WordWrap DAX Function

## 📖 Overview

`WordWrap` is a reusable DAX function that dynamically wraps text into lines of a specified maximum length (`Chunk`) **without splitting words**.  
It supports:

- Strict enforcement of maximum line length (except for single words longer than the chunk size).
- Hyphen-aware wrapping (hyphenated words can break naturally across lines).
- Clean handling of whitespace and punctuation.

This function is especially useful in **Power BI** when you need to display long text fields in visuals with controlled line lengths.

---

## ⚙️ Function Signature

```DAX
WordWrap = 
    (texttosplice as string, Chunk as int64) => ...
```

- **`texttosplice`**: The input text string to wrap.
- **`Chunk`**: Maximum number of characters per line.

---

## 🧩 How It Works

1. **Preprocessing**
   - Normalizes whitespace and punctuation.
   - Replaces hyphens (`-`) with `-` so they act as breakable tokens.

2. **Tokenization**
   - Splits text into words using a delimiter.
   - Builds a word table with lengths.

3. **Cumulative Length Tracking**
   - Calculates the running length of each line including spaces.

4. **Break Detection**
   - Checks if adding the next word would exceed the `Chunk`.
   - If so, starts a new line.

5. **Grouping**
   - Assigns words to line groups based on breaks.

6. **Concatenation**
   - Reassembles words into lines.
   - Restores hyphens by collapsing `" - "` back into `"-"`.

7. **Output**
   - Returns the wrapped text as a single string with line breaks (`UNICHAR(10)`).

---

## ✅ Example Usage

```DAX
EVALUATE
ROW(
    "Wrapped",
    WordWrap("State-of-the-art technology is evolving rapidly.", 20)
)
```

**Output:**

```
State-of-the-
art technology is
evolving rapidly.
```

---

## 🔍 Edge Cases

- **Oversized words**: If a single word is longer than `Chunk`, it will exceed the limit but remain intact.
- **Hyphenated words**: Break naturally at hyphens, with the hyphen visually preserved.
- **Multiple spaces**: Collapsed into a single space.
- **Punctuation**: Preserved, but semicolons and commas are normalized to spaces during preprocessing.

---

## 📊 Testing

The function has been validated with:

- 30+ sample texts of varying complexity.
- Hyphenated words (`state-of-the-art`, `long-term`).
- Oversized tokens (`Supercalifragilisticexpialidocious`).
- Mixed punctuation and spacing.

Line lengths are guaranteed to be ≤ `Chunk` (except oversized words).

---

## 🚀 Performance Notes

- Works well for moderate text lengths in Power BI visuals.
- For very large text fields or bulk processing, consider preprocessing in Power Query (M) for efficiency.

---

## 📌 License

This function is provided as-is for use in Power BI models.  
Feel free to adapt and extend it for your own scenarios.
