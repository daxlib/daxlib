# EG.ReferentialIntegrity

Generates a DAX query string that, when evaluated, returns a table listing referential integrity violations across all model relationships — identifying fact-side values with no matching entry in the related dimension.

Full explanation of the logic: [Referential Integrity Monitor for Power BI Reports](https://www.linkedin.com/pulse/referential-integrity-monitor-power-bi-reports-erick-oliveira-gghuf/)

## Functions

### `EG.ReferentialIntegrity.Monitor`

Inspects all relationships in the model via `INFO.VIEW.RELATIONSHIPS()` and returns a single-row table whose only column contains a ready-to-evaluate DAX expression. Evaluating that expression produces a table with one row per relationship, showing the invalid values and their count.

**Parameters**

None.

**Returns** `table` — one row with column `Referential_Integrity` containing the generated DAX query string.

**Usage**

**Step 1** — Run the function in DAX Studio to get the generated DAX code:

```dax
EVALUATE
EG.ReferentialIntegrity.Monitor ()
```

**Step 2** — Copy the value from the `Referential_Integrity` column.

**Step 3** — In Power BI Desktop, create a new **Calculated Table** and paste the copied expression as its definition. This table will automatically refresh and monitor all model relationships, showing which values in fact tables have no matching entry in the related dimension.

Once the calculated table exists, you can build a dedicated report page on top of it — for developers or end users — to continuously track referential integrity issues across the entire model.

The calculated table returns the following columns per relationship:

| Column | Description |
|---|---|
| `ID` | Relationship ID |
| `FromTable` / `FromColumn` | Fact-side table and column |
| `ToTable` / `ToColumn` | Dimension-side table and column |
| `FromCardinality` / `ToCardinality` | Relationship cardinality |
| `IsActive` | Whether the relationship is active |
| `Direction` | Cross-filtering direction |
| `FromTo` | Relationship description |
| `Invalid Values` | Pipe-delimited list of unmatched values |
| `Invalid Rows` | Count of unmatched values |

## Notes

- Uses `INFO.VIEW.RELATIONSHIPS()` to dynamically discover all model relationships at query time
- Skips Many-to-Many relationships with bidirectional cross-filtering
- For active One-to-Many relationships: detects fact-side values with no dimension match via `CALCULATETABLE` + `ISBLANK`
- For inactive, One-to-One, or Many-to-Many relationships: detects mismatches via `EXCEPT`
