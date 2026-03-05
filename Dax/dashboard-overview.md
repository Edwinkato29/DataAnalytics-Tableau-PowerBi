# Dashboard Design Guide
## Financial Performance Dashboard — Power BI

**Author:** Edwin Kato | Senior BI Developer

---

## Dashboard Structure

The dashboard is organized into five report pages, each targeting a specific audience and business question. All pages share a consistent header bar, color palette, and a period slicer synchronized across all visuals.

---

## Global Design Standards

### Color Palette

| Color | Hex | Usage |
|-------|-----|-------|
| Navy Blue | `#1a3a6b` | Header bars, primary KPI background |
| Emerald Green | `#0e6655` | Positive variance, on/above target |
| Amber | `#e67e22` | Neutral / approaching threshold |
| Red | `#c0392b` | Negative variance, over budget, declining |
| Light Gray | `#f5f7fa` | Page background |
| White | `#ffffff` | Card and visual backgrounds |
| Gold Accent | `#f0c040` | Highlight labels on dark backgrounds |

### Typography
- **Headers:** Segoe UI Semibold, 16–18pt
- **KPI Values:** Segoe UI Bold, 28–36pt
- **Body / Labels:** Segoe UI Regular, 11–12pt

### Shared Slicers (synced across all pages)
- **Fiscal Year** (dropdown)
- **Fiscal Quarter** (button slicer: Q1 | Q2 | Q3 | Q4)
- **Entity** (dropdown, defaulting to All)

---

## Page 1 — Executive Overview

**Audience:** CFO, CEO, Board  
**Purpose:** Single-screen summary of financial health

### Visuals

**Row 1 — KPI Cards (4 cards across top):**

| Card | Measure | Format | KPI Goal |
|------|---------|--------|----------|
| Total Revenue | `[Total Revenue]` | $M, 1 decimal | ≥ Budget |
| Total Expenses | `[Total Expenses]` | $M, 1 decimal | ≤ Budget |
| Gross Profit | `[Gross Profit]` | $M, 1 decimal | Maximize |
| Gross Margin % | `[Gross Margin %]` | %, 1 decimal | ≥ 40% |

Each card includes a **trend sparkline** using the prior period value and a **variance badge** showing `[Revenue Budget Variance %]` with conditional color formatting.

**Row 2 — Performance vs Budget (Clustered Bar + Line combo):**
- Bars: Actual Revenue by Quarter (grouped by entity)
- Line: Budget Revenue by Quarter
- X-axis: FiscalQuarter | Y-axis: $USD
- Data Labels: ON for actuals; toggle for budget line

**Row 3 — Expense Breakdown (Donut Chart) + Revenue by Entity (Treemap):**
- Donut: Total Expenses by Account Category (Operating/Non-Cash)
- Treemap: Revenue by Entity with size = amount, color = YoY growth %

---

## Page 2 — Revenue Deep Dive

**Audience:** VP Sales, Revenue Operations, FP&A  
**Purpose:** Understand revenue composition and trends

### Visuals

**Actual vs Budget Revenue by Month (Line & Clustered Column):**
- Columns: Monthly actual revenue
- Line: Monthly budget
- Color: Columns change to red if below budget

**Revenue Decomposition Tree:**
```
Total Revenue
└── By Entity (ENT-001, ENT-002, ENT-003)
    └── By Department
        └── By Account Name
            └── By Cost Center
```
Drill path is enabled for exploratory analysis.

**Top Accounts Table (Matrix Visual):**

| Account Name | Q1 Revenue | Q2 Revenue | Q3 Revenue | YTD | % of Total |
|---|---|---|---|---|---|
| Enterprise License | ... | ... | ... | ... | ... |
| Product Sales | ... | ... | ... | ... | ... |

- Conditional formatting on YTD column (data bars)
- Rank column using `[Revenue Rank by Department]` measure

**Revenue Trend Gauge:**
- Shows `[Revenue YoY Growth %]` on a gauge visual
- Target needle = 0% (flat year-over-year)
- Green zone: above 5% | Amber: 0–5% | Red: negative

---

## Page 3 — Expense Analysis

**Audience:** Finance Controllers, Department Heads, FP&A  
**Purpose:** Track expense trends and surface budget overruns

### Visuals

**Expense by Department & Cost Type (Stacked Bar):**
- X-axis: DepartmentName
- Y-axis: Amount
- Stack segments: Fixed / Variable / Non-Cash (colored by CostType)
- Budget reference line per department

**Cost Center Budget Matrix (Table):**

| Cost Center | Budget Owner | Budget | Actual | Variance | Variance % |
|---|---|---|---|---|---|
| Direct Sales - North | Sarah Mitchell | $X | $X | +$X | +3.2% |
| IT Infrastructure | Michael Barnes | $X | $X | -$X | -5.1% |

- Red conditional formatting on Variance % when negative (over budget)
- Bold for cost centers > 5% over budget

**Monthly Expense Trend (Line Chart):**
- Line 1: Actual expenses by month
- Line 2: Budget expenses by month
- Forecast line (dotted): Linear trend for remaining quarters

**Expense Distribution Pie:**
- Segments: Compensation / Technology / Marketing / G&A / Non-Cash
- Tooltip: shows % of total and YoY change

---

## Page 4 — Profitability & Margin Trends

**Audience:** CFO, FP&A Director  
**Purpose:** Deep analysis of profit and margin evolution

### Visuals

**Profit Bridge Waterfall Chart:**
```
Total Revenue (+)
→ Salaries & Wages (-)
→ Marketing (-)
→ IT Expenses (-)
→ G&A (-)
→ Depreciation (-)
→ Gross Profit (=)
```
- Green bars for revenue, red bars for expenses, blue total bar
- Data labels on all bars showing $ amounts

**Gross Margin % Trend (Area Chart):**
- X-axis: Month (Jan–Sep)
- Y-axis: Gross Margin %
- Reference band: 40–50% target zone (shaded in light green)
- Current period highlighted with data callout

**EBITDA & EBITDA Margin Table:**

| Period | EBITDA | EBITDA Margin % | vs. Prior Year |
|--------|--------|----------------|----------------|
| Q1 2024 | $X | X.X% | ▲ X.X% |
| Q2 2024 | $X | X.X% | ▲ X.X% |
| Q3 2024 | $X | X.X% | ▲ X.X% |

**Revenue vs Expense Running Totals (Dual-Axis Line):**
- Primary axis: Revenue running total (blue line)
- Secondary axis: Expense running total (red line)
- Spread between lines = cumulative gross profit

---

## Page 5 — Entity & Regional Consolidation

**Audience:** CFO, Controller, Group Finance  
**Purpose:** Multi-entity consolidated view for group reporting

### Visuals

**Entity Performance Matrix:**

| Entity | Revenue | Expenses | Gross Profit | Margin % | Budget Var % |
|--------|---------|----------|-------------|----------|-------------|
| Corporate HQ | $X | $X | $X | X% | +X% |
| Western Region | $X | $X | $X | X% | +X% |
| Eastern Region | $X | $X | $X | X% | +X% |
| **Total** | **$X** | **$X** | **$X** | **X%** | **+X%** |

- Row totals use `CALCULATE` with `ALL(dim_entities)` for group total
- Subtotals enabled on matrix visual

**Revenue Share by Entity (100% Stacked Bar by Quarter):**
- Shows how entity revenue composition shifts quarter by quarter
- Each segment = one entity, colored distinctly

**Entity Profitability Scatter Plot:**
- X-axis: Revenue (size of bubble)
- Y-axis: Gross Margin %
- Bubbles colored by entity
- Reference lines at average revenue and average margin

---

## Slicer & Filter Configuration

### Page-Level Filters
Each page includes a collapsible filter pane with:
- TransactionType (Revenue / Expense toggle)
- AccountCategory multi-select
- FiscalYear dropdown

### Cross-Page Slicer Sync
All period slicers (Fiscal Quarter, Entity) are synced across pages via:  
**View → Sync Slicers** → Enable "Sync" for all 5 pages

### Drill-Through Setup
From any chart, right-clicking on a department or entity navigates to a detail page showing all underlying transactions for that selection. Enable via:  
**Format → Page → Allow use as drill-through target**

---

## Report Publish & Access Settings

1. Publish to workspace: `Finance Analytics`
2. Set Row-Level Security (RLS) if department heads should only see their own department:
   - Create RLS role: `[DepartmentKey] = USERPRINCIPALNAME()` (requires user mapping table)
3. Share dashboard as **App** with Finance team
4. Enable **Scheduled Refresh:** Daily at 6:00 AM (requires on-premise data gateway if using local CSVs)

---

*Edwin Kato · Financial Performance Dashboard · Power BI · FY2024*
