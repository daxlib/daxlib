# Ankush.SemanticInfo

**A DAX library that returns semantic model information in a concise, easy-to-read format—designed to help with auditing semantic models and debugging Power BI reports.**

---

## Why Ankush.SemanticInfo?

Ever found yourself clicking through Power BI's UI trying to figure out what's actually in your model? Or spent hours tracing dependencies before changing a column? Ankush.SemanticInfo solves these problems by giving you instant, structured insights into your semantic model—all from simple DAX queries.

Whether you're documenting a complex model, debugging a report issue, or performing impact analysis before a change, this library puts everything you need at your fingertips.

## What It Does

Ankush.SemanticInfo extracts and presents semantic model metadata in clean, readable tables. It works with any Power BI semantic model and automatically filters out internal Power BI tables, giving you only the information that matters.

**Key capabilities:**
- **Model Inventory** – Get a complete overview of tables, columns, measures, relationships, and more
- **Impact Analysis** – See which calculations depend on a specific column or measure before you change it
- **Security Auditing** – Review all RLS roles and their filter expressions
- **Hidden Object Discovery** – Quickly find hidden tables, columns, or measures
- **Relationship Mapping** – Visualize all relationships with cardinality and direction indicators

## Use Cases

### 🔍 Model Auditing & Documentation
Need to document your model? Get a complete inventory in seconds:
```dax
EVALUATE Ankush.SemanticInfo.Model.Describe()
```

### 🐛 Debugging Reports
Something not working? Find hidden objects, invalid measures, or relationship issues:
```dax
EVALUATE Ankush.SemanticInfo.HiddenObjects.Display("Measures")
```

### ⚠️ Impact Analysis
About to rename a column? See what will break first:
```dax
EVALUATE Ankush.SemanticInfo.DAX_Impact.Show("Sales", "Amount")
```

### 🔐 Security Review
Auditing RLS? Get all roles and their filter expressions:
```dax
EVALUATE Ankush.SemanticInfo.RLS.Display()
```

### 📊 Table Deep-Dive
Need detailed column information for a specific table?
```dax
EVALUATE Ankush.SemanticInfo.Tables.Describe(ALL(Sales))
```

## Visual Indicators

The library uses intuitive emoji indicators for quick scanning:
- 👁️‍🗨 = Hidden object
- ƒ𝑥 = Calculated column/table
- ❌ = Invalid measure
- 1️⃣/*️⃣ = Relationship cardinality
- 🟢/⚫ = Active/Inactive relationship

## Model Independence

All functions are model-independent—they work with any Power BI semantic model without requiring specific table or column names. Just import the library and start querying.

## Getting Started

1. Import the library into your Power BI semantic model using DAX View
2. Start exploring with `Ankush.SemanticInfo.Explain()` to see all available functions
3. Use the examples above to get started

## Author

**Ankush Sharma**

- LinkedIn: [ankush-sharma-00007](https://www.linkedin.com/in/ankush-sharma-00007/)
- GitHub: [Ankush-Sharma06/Ankush.SemanticInfo](https://github.com/Ankush-Sharma06/Ankush.SemanticInfo)

---

**Version:** 1.0.0