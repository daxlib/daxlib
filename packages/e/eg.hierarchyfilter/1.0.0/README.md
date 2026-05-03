# EG.HierarchyFilter

Function to filter a parent-child hierarchy by one or more selected parent employees, returning every subordinate in their chain — including intermediate managers who are parents of others.

## What it does

`EG.HierarchyFilter.Compute` evaluates a measure restricted to the employees who belong to the hierarchy chain of the selected parent(s). **Works with single or multiple parent selections.** It is recommended to use this function inside a **Calculation Group**, where `SELECTEDMEASURE()` passes any base measure through automatically — without rewriting every measure in the model.

---

## Function

```DAX
EG.HierarchyFilter.Compute (
    <valueExpr>,                    -- e.g., [Number of Employees]
    <employeeKeyColumn>,            -- e.g., Employees[EmployeeKey]
    <parentEmployeeColumn>,         -- e.g., Employees[ParentEmployeeKey]
    <pathColumn>,                   -- e.g., Employees[Path]  -- pre-computed via PATH()
    <parentEmployeeFilterColumn>    -- e.g., 'Parent'[ParentEmployeeKey]
)
```

**Return:** The result of `<valueExpr>` calculated only for employees whose `<pathColumn>` contains the selected parent key(s). With no parent selected, the unfiltered expression is returned unchanged.

**Assumptions:** The `<pathColumn>` must be pre-computed with the DAX `PATH()` function, which produces pipe-delimited ancestor paths (e.g., `"John|Mary|Sarah"`). The filter column (`<parentEmployeeFilterColumn>`) must come from a disconnected table listing unique parent keys — no relationship to the employee table.

---

## Model requirements

1. **Employee table:** Must contain at least three columns — a unique key, an immediate parent key, and a pre-computed PATH column.

   ```DAX
   Employees = 
   DATATABLE (
       "EmployeeKey",       STRING,
       "ParentEmployeeKey", STRING,
       "Path",              STRING, -- Obtained using the PATH function
       {
           { "John",   "John",   "John" },
           { "Mary",   "John",   "John|Mary" },
           { "David",  "John",   "John|David" },
           { "Sarah",  "Mary",   "John|Mary|Sarah" },
           { "James",  "Mary",   "John|Mary|James" },
           { "Robert", "David",  "John|David|Robert" },
           { "Maria",  "Robert", "John|David|Robert|Maria" }
       }
   )
   ```

   In a real model, the `Path` column is a calculated column defined as:

   ```DAX
   Path = PATH ( Employees[EmployeeKey], Employees[ParentEmployeeKey] )
   ```

2. **Filter table (disconnected):** Lists every unique parent key. No relationship to the Employee table.

   ```DAX
   Parent = ALLNOBLANKROW ( Employees[ParentEmployeeKey] )
   ```

---

## Basic usage

```DAX
Hierarchy Filter =
EG.HierarchyFilter.Compute (
    SELECTEDMEASURE (),
    Employees[EmployeeKey],
    Employees[ParentEmployeeKey],
    Employees[Path],
    'Parent'[ParentEmployeeKey]
)
```

Place this expression as a **Calculation Item** inside a Calculation Group. When a user selects one or more values from a slicer on `Parent[ParentEmployeeKey]`, every measure in the model is automatically restricted to the chosen hierarchy chain(s).

For example, selecting **Mary** filters to: Mary, Sarah, James.  
Selecting **David** filters to: David, Robert, Maria.  
Selecting **Mary** and **David** together filters to: Mary, Sarah, James, David, Robert, Maria.
