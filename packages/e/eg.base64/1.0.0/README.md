# EG.Base64

Decode Base64 encoded strings to plain text using pure DAX, with no external dependencies.

## Functions

### `EG.Base64.Decode`

Converts a Base64 encoded string to its plain text representation.

**Parameters**

| Parameter | Type   | Description                  |
|-----------|--------|------------------------------|
| Base64    | string | The Base64 encoded string    |

**Returns** `string` — the decoded plain text.

**Example**

```dax
EG.Base64.Decode ( "SGVsbG8gV29ybGQ=" )
-- Returns: "Hello World"
```

---

### Helper functions

These functions are used internally by `EG.Base64.Decode` and are not intended to be called directly.

| Function                    | Description                                              |
|-----------------------------|----------------------------------------------------------|
| `EG.Base64.ConvertASCII`    | Maps a Base64 alphabet character to its 6-bit value (0–63) |
| `EG.Base64.BytePartition`   | Reconstructs 3 bytes from 4 Base64 6-bit values         |

## Notes

- Supports standard Base64 alphabet (RFC 4648): `A–Z`, `a–z`, `0–9`, `+`, `/`
- Padding characters (`=`) are handled as blank characters and ignored in the output
- Result is a single string value
