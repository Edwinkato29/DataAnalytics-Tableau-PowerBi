# Building the Finance Semantic Model in Power BI

> **Project:** Financial Performance Dashboard  
> **Tool:** Power BI Desktop (Semantic Model / Dataset)  
> **Author:** Edwin Kato | Senior BI Developer  
> **Schema:** Star Schema · 1 Fact Table · 5 Dimension Tables

---

## Overview

A semantic model (formerly called a Power BI dataset) is the analytical foundation of any Power BI report. It defines how raw data sources are transformed, related, and enriched with business logic through DAX measures. This document outlines every step taken to build the finance semantic model from raw CSV sources to a fully functional, production-ready analytical layer.

---

## Step 1 — Connect Data Sources in Power Query

### 1.1 Load all six CSV files

Open Power BI Desktop → **Home → Get Data → Text/CSV** and load each of the following files:

| File | Load Name |
|------|-----------|
| `fact_transactions.csv` | `fact_transactions` |
| `dim_date.csv` | `dim_date` |
| `dim_accounts.csv` | `dim_accounts` |
| `dim_departments.csv` | `dim_departments` |
| `dim_cost_centers.csv` | `dim_cost_centers` |
| `dim_entities.csv` | `dim_entities` |

### 1.2 Open Power Query Editor

**Home → Transform Data** opens the Power Query Editor where all transformations are applied before loading to the model.

---

## Step 2 — Clean & Transform Data in Power Query

### 2.1 Promote Headers

For each table, verify the first row is used as headers:  
**Home → Use First Row as Headers** (if not already applied automatically).

### 2.2 Set Correct Data Types

Correct data types prevent errors in relationships and DAX calculations.

**`fact_transactions` column types:**

| Column | Data Type |
|--------|-----------|
| TransactionID | Text |
| DateKey | Whole Number |
| Amount | Decimal Number |
| BudgetAmount | Decimal Number |
| FiscalYear | Whole Number |
| FiscalQuarter | Text |

**`dim_date` column types:**

| Column | Data Type |
|--------|-----------|
| DateKey | Whole Number |
| FullDate | Date |
| Year | Whole Number |
| Month | Whole Number |
| WeekOfYear | Whole Number |
| IsWeekend | True/False |
| IsHoliday | True/False |

### 2.3 Remove Blank / Error Rows

On each table: **Home → Remove Rows → Remove Blank Rows**  
Then: **Home → Remove Rows → Remove Errors**

### 2.4 Add a Variance Column (Power Query)

In `fact_transactions`, add a calculated column for Budget Variance:

1. **Add Column → Custom Column**
2. Name: `BudgetVariance`
3. Formula: `= [Amount] - [BudgetAmount]`

### 2.5 Trim & Clean Text Columns

For all text dimension columns (account names, department names, etc.):  
Select column → **Transform → Format → Trim**, then **→ Clean**

### 2.6 Close & Apply

**Home → Close & Apply** loads all cleaned tables into the data model.

---

## Step 3 — Build the Star Schema Relationships

This is the most critical step. Navigate to the **Model View** (left rail icon in Power BI Desktop).

### 3.1 Relationship: fact_transactions → dim_date

| Setting | Value |
|---------|-------|
| From Table | `fact_transactions` |
| From Column | `DateKey` |
| To Table | `dim_date` |
| To Column | `DateKey` |
| Cardinality | Many to One (M:1) |
| Cross-filter | Single |
| Active | ✅ Yes |

### 3.2 Relationship: fact_transactions → dim_accounts

| Setting | Value |
|---------|-------|
| From Column | `AccountKey` |
| To Column | `AccountKey` |
| Cardinality | Many to One (M:1) |
| Cross-filter | Single |
| Active | ✅ Yes |

### 3.3 Relationship: fact_transactions → dim_departments

| Setting | Value |
|---------|-------|
| From Column | `DepartmentKey` |
| To Column | `DepartmentKey` |
| Cardinality | Many to One (M:1) |
| Cross-filter | Single |
| Active | ✅ Yes |

### 3.4 Relationship: fact_transactions → dim_cost_centers

| Setting | Value |
|---------|-------|
| From Column | `CostCenterKey` |
| To Column | `CostCenterKey` |
| Cardinality | Many to One (M:1) |
| Cross-filter | Single |
| Active | ✅ Yes |

### 3.5 Relationship: fact_transactions → dim_entities

| Setting | Value |
|---------|-------|
| From Column | `EntityKey` |
| To Column | `EntityKey` |
| Cardinality | Many to One (M:1) |
| Cross-filter | Single |
| Active | ✅ Yes |

> **Best Practice:** Always use integer surrogate keys (like `DateKey = 20240101`) for relationship joins. They perform significantly faster than string-based joins and use less memory.

---

## Step 4 — Configure the Date Table

Power BI requires a designated date table for time intelligence functions (YTD, MTD, prior period, etc.).

### 4.1 Mark `dim_date` as a Date Table

1. Click on the `dim_date` table in Model View
2. **Table Tools → Mark as Date Table**
3. Set the date column to `FullDate`
4. Click **OK**

This step is **required** before any `DATEYTD`, `SAMEPERIODLASTYEAR`, or `DATEADD` DAX functions will work correctly.

### 4.2 Sort Month by Number

1. Go to **Data View**, select `dim_date`
2. Click on `MonthName` column
3. **Column Tools → Sort by Column → Month** (the numeric column)

This ensures months sort as Jan → Dec, not alphabetically.

---

## Step 5 — Create Calculated Columns (DAX)

Calculated columns are added in the **Data View** using the formula bar.

### 5.1 In `fact_transactions` — Add Profit/Loss Flag

```dax
PnL_Flag = 
IF([TransactionType] = "Revenue", "Inflow", "Outflow")
```

### 5.2 In `fact_transactions` — Budget Variance %

```dax
BudgetVariancePct = 
DIVIDE([Amount] - [BudgetAmount], [BudgetAmount], 0)
```

### 5.3 In `dim_date` — Quarter-Year Label

```dax
QuarterLabel = dim_date[FiscalYear] & " " & dim_date[FiscalQuarter]
```

### 5.4 In `dim_accounts` — Simplified Type Label

```dax
IncomeStatement_Section = 
SWITCH(
    dim_accounts[AccountCategory],
    "Revenue", "Revenue",
    "Expense", "Operating Expenses",
    "Non-Cash", "Non-Cash Items",
    "Other"
)
```

---

## Step 6 — Build the DAX Measure Library

All DAX measures should be stored in a **dedicated measures table** for organization. Create this by:

1. **Enter Data** → create a blank table named `_Measures`
2. Delete the default column → close
3. Add all measures to this table

### 6.1 Core Financial Measures

```dax
-- Total Revenue
Total Revenue = 
CALCULATE(
    SUM(fact_transactions[Amount]),
    dim_accounts[AccountCategory] = "Revenue"
)

-- Total Expenses
Total Expenses = 
CALCULATE(
    SUM(fact_transactions[Amount]),
    dim_accounts[AccountCategory] = "Expense"
)

-- Gross Profit
Gross Profit = [Total Revenue] - [Total Expenses]

-- Gross Margin %
Gross Margin % = 
DIVIDE([Gross Profit], [Total Revenue], 0)

-- Budget Total
Total Budget = SUM(fact_transactions[BudgetAmount])

-- Budget Variance
Budget Variance = [Total Revenue] - [Total Budget]

-- Budget Variance % 
Budget Variance % = 
DIVIDE([Budget Variance], [Total Budget], 0)
```

### 6.2 Time Intelligence Measures

```dax
-- Revenue Year-to-Date
Revenue YTD = 
CALCULATE(
    [Total Revenue],
    DATESYTD(dim_date[FullDate])
)

-- Revenue Previous Year
Revenue PY = 
CALCULATE(
    [Total Revenue],
    SAMEPERIODLASTYEAR(dim_date[FullDate])
)

-- Year-over-Year Revenue Growth
Revenue YoY Growth = 
DIVIDE([Total Revenue] - [Revenue PY], [Revenue PY], 0)

-- Revenue Month-to-Date
Revenue MTD = 
CALCULATE(
    [Total Revenue],
    DATESMTD(dim_date[FullDate])
)

-- Revenue Quarter-to-Date
Revenue QTD = 
CALCULATE(
    [Total Revenue],
    DATESQTD(dim_date[FullDate])
)
```

### 6.3 KPI & Conditional Measures

```dax
-- Expense Ratio
Expense Ratio = 
DIVIDE([Total Expenses], [Total Revenue], 0)

-- Revenue per Transaction
Avg Transaction Value = 
DIVIDE(
    [Total Revenue],
    CALCULATE(COUNTROWS(fact_transactions), dim_accounts[AccountCategory] = "Revenue"),
    0
)

-- Budget Attainment Color (for conditional formatting)
Budget Attainment Color = 
SWITCH(
    TRUE(),
    [Budget Variance %] >= 0.05, "#27ae60",   -- Green: 5%+ over budget
    [Budget Variance %] >= 0, "#f39c12",       -- Amber: On target
    "#e74c3c"                                   -- Red: Under budget
)
```

---

## Step 7 — Configure Display Formatting

### 7.1 Format Measures

Select each measure → **Measure Tools → Format**:

| Measure | Format |
|---------|--------|
| Total Revenue | Currency ($) · 0 decimals |
| Gross Margin % | Percentage · 1 decimal |
| Budget Variance % | Percentage · 1 decimal |
| Revenue YoY Growth | Percentage · 1 decimal |
| Avg Transaction Value | Currency ($) · 0 decimals |

### 7.2 Set Home Table

For each measure in `_Measures`, ensure the **Home Table** is set to `_Measures` to keep the field list clean.

### 7.3 Add Descriptions to Measures

Right-click each measure → **Properties** → add a plain-language description. These appear as tooltips in report view and in external tools.

---

## Step 8 — Optimize the Model for Performance

### 8.1 Hide Foreign Keys from Report View

In the `fact_transactions` table, right-click each `*Key` column (except the PK) → **Hide in Report View**. Users should interact with descriptive names from dimension tables, not raw key columns.

### 8.2 Hide Redundant Dimension Columns

In dimension tables, hide technical columns (e.g., `DateKey` in `dim_date`) — users should filter by `FullDate`, `MonthName`, etc.

### 8.3 Set Sort Columns

| Table | Column | Sort By |
|-------|--------|---------|
| dim_date | MonthName | Month |
| dim_date | DayName | DayOfWeek |
| dim_date | QuarterName | Quarter |

### 8.4 Enable Column Statistics (Optional)

**File → Options → Preview Features → Column profile** — helps identify data quality issues in Power Query.

---

## Step 9 — Publish to Power BI Service

1. **File → Publish → Publish to Power BI**
2. Select your workspace (e.g., `Finance Analytics`)
3. Once published, navigate to **Power BI Service → Datasets**
4. Configure **Scheduled Refresh** (if using a live data gateway)
5. Set data source credentials under **Dataset Settings → Data source credentials**

---

## Step 10 — Final Model Validation Checklist

| Check | Status |
|-------|--------|
| All 5 relationships are active M:1 | ✅ |
| dim_date marked as Date Table | ✅ |
| Time intelligence measures return correct values | ✅ |
| FK columns hidden from report view | ✅ |
| Measures stored in `_Measures` table | ✅ |
| Month/Day sort columns configured | ✅ |
| Gross Margin % = Revenue − Expenses ÷ Revenue | ✅ |
| YTD/YoY measures cross-validate with raw data | ✅ |
| Model published to Power BI Service | ✅ |
| Refresh schedule configured | ✅ |

---

## Model Summary

| Component | Count |
|-----------|-------|
| Tables | 6 (1 fact + 5 dims) |
| Relationships | 5 active M:1 |
| DAX Measures | 15+ |
| Calculated Columns | 5 |
| Source Rows (Fact) | 100 |

---

*Built by Edwin Kato · Senior BI Developer · Power BI Semantic Model v1.0*
