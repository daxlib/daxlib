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

## Notes

- Supports standard Base64 alphabet (RFC 4648): `A–Z`, `a–z`, `0–9`, `+`, `/`
- Padding characters (`=`) are handled as blank characters and ignored in the output
- Result is a single string value
- Version 1.0.1 reimplements `EG.Base64.Decode` as a single self-contained function (alphabet lookup via `FIND` instead of a `SWITCH`-based helper). The public function signature and behavior are unchanged from 1.0.0; the internal helper functions `EG.Base64.ConvertASCII` and `EG.Base64.BytePartition` no longer exist in this version.
