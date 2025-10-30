# Gld.TextUtils.WordWrap — README (v1.0.2)

## Overview of Main Functions

### `Gld.TextUtils.WordWrap`
Wraps a given text string into multiple lines, ensuring that each line does not exceed a specified character limit (`Chunk`). It respects word boundaries and avoids splitting words mid‑way. The output is a single string with line breaks (`UNICHAR(10)`), suitable for display in visuals, tooltips, or formatted exports. Performance is fast and lightweight.

### `Gld.TextUtils.WordWrapWithPasses`
An enhanced wrapper around `WordWrap` that applies multiple optimization passes to compact the output further. It first performs a bidirectional pass (splitting long lines and pulling words up), followed by up to nine additional pull‑up passes to reduce line count and improve space utilization. This achieves stricter line‑length enforcement at a higher computational cost.

---

## Limitations (v1.0.2)

### Shared
- No support for preserving hard line breaks (original CR/LF are normalized away).
- No handling of non‑breaking spaces or special Unicode spacing.
- No language‑aware hyphenation or sentence‑level semantics.
- Words longer than `Chunk` are not broken; they will exceed the limit.

### `WordWrap`
- May produce lines longer than `Chunk` if a single word exceeds the limit.
- No post‑processing optimization; line grouping is greedy and static.

### `WordWrapWithPasses`
- Maximum of 10 passes; additional passes require extension.
- Pull‑up logic only considers the first word of the next line.
- May produce lines with low word density at lower pass counts.
- Increased performance cost with longer input and higher pass counts.

---

## Comparison of Results

- **Line‑length behavior**: `WordWrap` uses a soft limit and can yield lines over `Chunk` in edge cases, whereas `WordWrapWithPasses` enforces a hard limit (subject to very long single words).
- **Compaction**: `WordWrapWithPasses` progressively compacts the layout as `Passes` increases, but early passes may leave sparse lines.
- **Performance**: `WordWrap` is faster and suitable for large‑scale use. `WordWrapWithPasses` is more computationally expensive but offers tighter control.

---

## Example Usage (Chunk = 20, “Moby‑Dick” — First Paragraph)

| demotextwraped | demotextwrapedwithpasses1 | demotextwrapedwithpasses5 | demotextwrapedwithpasses10 |
|---|---|---|---|
| Call me Ishmael.<br>Some years ago—never<br>mind how long<br>precisely—having little or<br>no money in my purse,<br>and nothing<br>particular to interest me<br>on shore, I thought I<br>would sail about a<br>little and see the<br>watery part of the<br>world. It is a way I<br>have of driving off<br>the spleen and<br>regulating the<br>circulation. Whenever I find<br>myself growing grim<br>about the mouth;<br>Whenever It is a damp,<br>drizzly November in my<br>soul; Whenever I<br>find myself<br>involuntarily pausing before<br>coffin warehouses,<br>and bringing up the<br>rear of every funeral<br>I meet; and<br>especially Whenever my hypos<br>get such an upper<br>hand of me, that It<br>requires a strong<br>moral principle to<br>prevent me from<br>deliberately stepping into<br>the street, and<br>methodically knocking<br>people’s hats off—then,<br>I account It high<br>time to get to sea as<br>soon as I can. | Call me Ishmael.<br>Some years ago—never<br>mind how long<br>precisely—having little<br>or no money in my<br>purse, and nothing<br>particular<br>to interest<br>me on<br>shore, I thought I<br>would sail about<br>a little<br>and see the<br>watery part of<br>the world. It<br>is a way I have<br>of driving off<br>the spleen and<br>regulating the<br>circulation. Whenever I<br>find myself growing<br>grim about the<br>mouth; Whenever It<br>is a damp, drizzly<br>November<br>in my soul;<br>Whenever I find<br>myself involuntarily<br>pausing before<br>coffin warehouses,<br>and<br>bringing up the rear<br>of every funeral I<br>meet; and<br>especially Whenever<br>my hypos get<br>such an upper<br>hand of me, that<br>It requires a<br>strong moral<br>principle to<br>prevent me from<br>deliberately stepping<br>into the street, and<br>methodically<br>knocking people’s<br>hats off—then, I<br>account It high time<br>to get to<br>sea as soon as<br>I can. | Call me Ishmael.<br>Some years ago—never<br>mind how long<br>precisely—having little<br>or no money in my<br>purse, and nothing<br>particular<br>to interest<br>me on<br>shore, I thought I<br>would sail about<br>a little<br>and see the<br>watery part of<br>the world. It<br>is a way I have<br>of driving off<br>the spleen and<br>regulating the<br>circulation. Whenever I<br>find myself growing<br>grim about the<br>mouth; Whenever It<br>is a damp, drizzly<br>November<br>in my soul;<br>Whenever I find<br>myself involuntarily<br>pausing before<br>coffin warehouses,<br>and<br>bringing up the rear<br>of every funeral I<br>meet; and<br>especially Whenever<br>my hypos get<br>such an upper<br>hand of me, that<br>It requires a<br>strong moral<br>principle to<br>prevent me from<br>deliberately stepping<br>into the street, and<br>methodically<br>knocking people’s<br>hats off—then, I<br>account It high time<br>to get to<br>sea as soon as<br>I can. | Call me Ishmael.<br>Some years ago—never<br>mind how long<br>precisely—having little<br>or no money in my<br>purse, and nothing<br>particular to<br>interest me on<br>shore, I thought I<br>would sail about a<br>little and see<br>the watery part<br>of the world.<br>It is a<br>way I have<br>of driving off<br>the spleen<br>and<br>regulating the<br>circulation. Whenever I<br>find myself growing<br>grim about the<br>mouth; Whenever It<br>is a damp, drizzly<br>November in my soul;<br>Whenever I find<br>myself involuntarily<br>pausing before<br>coffin warehouses,<br>and bringing up the<br>rear of every<br>funeral I meet; and<br>especially<br>Whenever my hypos<br>get such an upper<br>hand of me, that<br>It requires a<br>strong moral<br>principle to prevent<br>me from<br>deliberately<br>stepping into the<br>street, and<br>methodically<br>knocking people’s<br>hats off—then, I<br>account It high time<br>to get to sea as<br>soon as I can. |

---

## Breakdown of how the functions work

Please refer to the comments in the function