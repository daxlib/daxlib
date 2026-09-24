# EG.MultiValuedKeys

Function to find values that are associated with more than one identifier, helping detect duplicate registrations — the same entity (product, customer, supplier, etc.) recorded under different IDs.

## What it does

`EG.MultiValuedKeys.Keep` counts, for each distinct value of `<KeyColumn>`, how many distinct `<AttributeColumn>` values it is associated with. Values linked to a single attribute are discarded; values linked to two or more attributes are kept, along with every row that produced them and the resulting count.

---

## Function

```DAX
EG.MultiValuedKeys.Keep (
    <KeyColumn>,          -- e.g., Products[ProductName]   -- the column you want to check for duplicity
    <AttributeColumn>     -- e.g., Products[ProductId]      -- counts how many distinct attributes the key has
)
```

**Return:** A table with the original `<KeyColumn>` and `<AttributeColumn>` columns, plus an `@RowsCount` column, containing only the rows whose `<KeyColumn>` value is associated with more than one distinct `<AttributeColumn>` value.

---

## Basic usage

```DAX
Duplicated Products =
EG.MultiValuedKeys.Keep ( Products[ProductName], Products[ProductId] )
```

Given:

| ProductId | ProductName |
|-----------|-------------|
| P001      | Notebook X  |
| P002      | Mouse Y     |
| P007      | Notebook X  |
| P009      | Monitor Z   |

Returns:

| ProductName | ProductId | @RowsCount |
|-------------|-----------|------------|
| Notebook X  | P001      | 2          |
| Notebook X  | P007      | 2          |

`Notebook X` is registered under two IDs (`P001` and `P007`), so both rows are kept. `Mouse Y` and `Monitor Z` each have a single ID and are dropped.
