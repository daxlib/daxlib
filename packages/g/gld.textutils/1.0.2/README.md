
# DAX Text Wrapping Utilities

A collection of DAX functions for text wrapping and formatting with different strategies for handling word boundaries, line breaks, and character limits.

## 📋 Functions Overview

| Function | Purpose | Best For |
|----------|---------|----------|
| `WordWrap` | Word-aware wrapping at character limit | General text, documents, readable output |
| `WordWrapWithBreak` | Character-level wrapping with word breaking | Fixed-width formatting, guaranteed limits |
| `WordWrapWithLF` | Preserves existing paragraph structure | Multi-paragraph documents, formatted text |

## 🚀 Installation

Copy all three function definitions into your DAX model. They are self-contained and have no external dependencies.

## 📖 Detailed Documentation

### 1. `Gld.TextUtils.WordWrap`

**Signature:** `WordWrap(texttosplice: string, Chunk: int64) => string`

Word-based wrapping that preserves word boundaries. Uses a cumulative character counting algorithm to group words into lines without exceeding the character limit.

**Features:**
- ✅ Preserves word boundaries (never breaks words)
- ✅ Handles common punctuation (commas, semicolons, hyphens, em dashes)
- ✅ Normalizes line breaks (CR/LF → spaces)
- ✅ Returns clean, trimmed lines

**Limitations:**
- ⚠️ Words longer than `Chunk` will still appear on one line (exceeding the limit)
- ⚠️ No hyphenation for long words
- ⚠️ Treats punctuation as part of word length

**Example:**
```dax
EVALUATE
{
    (
        "Wrapped Text",
        Gld.TextUtils.WordWrap(
            "To be, or not to be, that is the question.", 
            20
        )
    )
}
```

**Output:**
```
To be, or not to be,
that is the question.
```

### 2. `Gld.TextUtils.WordWrapWithBreak`

**Signature:** `WordWrapWithBreak(texttosplice: string, Chunk: int64) => string`

Simple character-level wrapping that guarantees lines never exceed the character limit. Breaks words with hyphens when necessary.

**Features:**
- ✅ Guarantees line length ≤ `Chunk`
- ✅ Breaks long words with hyphens
- ✅ Simple, predictable behavior
- ✅ Good for fixed-width requirements

**Limitations:**
- ⚠️ May create awkward word breaks
- ⚠️ Doesn't respect word boundaries
- ⚠️ Can break in middle of words

**Example:**
```dax
EVALUATE
{
    (
        "Fixed Width",
        Gld.TextUtils.WordWrapWithBreak(
            "Hello world", 
            3
        )
    )
}
```

**Output:**
```
Hel-
lo
wor-
ld
```

### 3. `Gld.TextUtils.WordWrapWithLF`

**Signature:** `WordWrapWithLF(texttosplice: string, Chunk: int64) => string`

Preserves existing paragraph structure while wrapping text within each paragraph. Ideal for multi-paragraph documents.

**Features:**
- ✅ Preserves original line/paragraph breaks
- ✅ Empty lines remain as paragraph separators
- ✅ Maintains document structure
- ✅ Uses `WordWrap` for intra-paragraph formatting

**Limitations:**
- ⚠️ Inherits limitations from `WordWrap`
- ⚠️ Multiple consecutive line breaks create multiple empty lines

**Example:**
```dax
EVALUATE
{
    (
        "Multi-paragraph",
        Gld.TextUtils.WordWrapWithLF(
            "First paragraph." & UNICHAR(10) & UNICHAR(10) & "Second paragraph.", 
            15
        )
    )
}
```

**Output:**
```
First paragraph.

Second paragraph.
```

## 🎯 Use Cases

### When to use each function:

| Scenario | Recommended Function | Why |
|----------|---------------------|-----|
| General document text | `WordWrap` | Preserves readability, respects words |
| Fixed-width displays | `WordWrapWithBreak` | Guarantees width, breaks if needed |
| Preserving formatting | `WordWrapWithLF` | Maintains paragraphs, headings |
| Code comments | `WordWrapWithBreak` | Exact column alignment |
| Literary text | `WordWrap` or `WordWrapWithLF` | Natural reading flow |
| User input with line breaks | `WordWrapWithLF` | Respects user's formatting |
| Data exports | `WordWrapWithBreak` | Consistent column widths |

## ⚙️ Algorithm Details

### `WordWrap` Algorithm:
1. **Normalization**: Convert CR/LF to spaces, add spacing after punctuation
2. **Tokenization**: Split into words using PATH functions
3. **Cumulative Calculation**: Track running character count with spaces
4. **Line Assignment**: Use `QUOTIENT((cumulative-1), Chunk)` for line grouping
5. **Line Building**: Concatenate words per line group
6. **Cleanup**: Remove temporary punctuation spacing

### `WordWrapWithBreak` Algorithm:
1. **Simple Split**: Divide text into fixed `Chunk`-sized segments
2. **Direct Extraction**: Take characters at exact positions
3. **Trimming**: Clean leading/trailing spaces
4. **Assembly**: Join with line feeds

### `WordWrapWithLF` Algorithm:
1. **Paragraph Detection**: Replace line breaks with markers
2. **Paragraph Separation**: Split into individual paragraphs
3. **Parallel Processing**: Apply `WordWrap` to each paragraph
4. **Reassembly**: Combine with original spacing

## 📊 Performance Considerations

1. **`WordWrap`**: Most computationally intensive due to PATH functions and cumulative calculations
2. **`WordWrapWithBreak`**: Fastest - simple character operations
3. **`WordWrapWithLF`**: Moderate - depends on paragraph count and `WordWrap` performance

**Tip**: For large texts (>10KB), consider preprocessing outside DAX or using simpler functions.

## 🐛 Known Issues & Limitations

1. **Long Words**: `WordWrap` doesn't break words longer than `Chunk`
2. **Punctuation**: Spacing after punctuation may affect line length calculations
3. **Unicode**: Wide characters (emojis, Asian characters) count as 1 character but may display wider
4. **Mixed Content**: HTML/tags within text are treated as regular characters

## 🔧 Testing Examples

```dax
// Test Suite
EVALUATE
{
    // Test 1: Basic wrapping
    ("Test 1 - Basic", Gld.TextUtils.WordWrap("The quick brown fox", 10)),
    
    // Test 2: Punctuation handling  
    ("Test 2 - Punctuation", Gld.TextUtils.WordWrap("Hello, world! How are you?", 15)),
    
    // Test 3: Paragraph preservation
    ("Test 3 - Paragraphs", Gld.TextUtils.WordWrapWithLF("Para1." & UNICHAR(10) & "Para2.", 10)),
    
    // Test 4: Word breaking
    ("Test 4 - Breaking", Gld.TextUtils.WordWrapWithBreak("abcdefghijklmnop", 5))
}
```

## 📝 Best Practices

1. **Choose the right function** for your use case
2. **Test with representative data** including edge cases
3. **Consider preprocessing** very long texts
4. **Validate `Chunk` parameter** (should be > 0)
5. **Handle empty strings** in calling code if needed

## 🔄 Version History

- **v1.0.0**: Initial release 
- **v1.0.1**: Added em dash (—) support in `WordWrap`
- **v1.0.2**: Improved comments and documentation

