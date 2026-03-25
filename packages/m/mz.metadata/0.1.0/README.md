# mz.metadata

Utility DAX user-defined functions for inspecting semantic model metadata.

## Included functions

- `GetTableColumnCount`

---

## GetTableColumnCount

Returns the number of physical columns in a model table by counting matching rows in `COLUMNSTATISTICS()`.

### Syntax

```DAX
GetTableColumnCount(<TableRef>)
