# 📊 Data Analytics Portfolio — Tableau & Power BI

**Senior BI Developer portfolio** showcasing production-grade dashboards across financial services, wealth management, supply chain, and HR analytics domains.
Each project demonstrates end-to-end BI development: data modeling, calculated metrics, visual design, and executive-ready storytelling.

---

## 👤 About

**Edwin Kato** — Senior BI Developer / Senior Tableau Developer with 8+ years of experience building enterprise analytics solutions across financial services and healthcare.
Currently at Oppenheimer Funds. Certified



---

## 📁 Repository Structure

```
DataAnalytics-Tableau-PowerBi/
│
├── PowerBI/
│   └── Employee_Hiring_and_History/
│       ├── Employee_Hiring_and_History.pbix
│       ├── Dataset/
│       └── README_EmployeeHiring.md
│
├── Tableau/
│   ├── Wealth_and_Banking/
│   │   ├── wealth_and_banking_Dashboard.twbx
│   │   └── README_WealthBanking.md
│   │
│   └── Inventory/
│       ├── Inventory.twbx
│       └── README_Inventory.md
│
└── README.md
```

---

## 🗂️ Projects

### 1. 🏦 Employee Hiring & History Dashboard — Power BI

**File:** `PowerBI/Employee_Hiring_and_History/Employee_Hiring_and_History.pbix`

A human resources analytics dashboard tracking workforce dynamics for a Sales & Marketing division. Built in Power BI with a star schema data model and DAX measure library.

| Attribute | Detail |
|---|---|
| **Tool** | Power BI (.pbix) |
| **Domain** | HR Analytics / Workforce Planning |
| **Data Model** | Star schema |
| **Key Metrics** | New Hires, Active Headcount, Separations, Bad Hire Rate |

**Dashboard Pages:**

- **New Hires** — Monthly hiring volume, full-time vs. part-time breakdown, and YoY % change vs. same period last year (SPLY)
- **Actives & Separations** — Active headcount trends, voluntary vs. involuntary turnover, separation YoY variance
- **Bad Hires** — Quality-of-hire rate as % of actives, breakdown by region and ethnicity, separation reason analysis
- **Tooltip** — On-hover detail page surfacing separation reasons in context

**Key DAX Measures:**
```dax
New Hires SPLY        -- Same period last year comparison
Seps YoY % Change     -- Year-over-year separation trend
BadHire%ofActives     -- Quality of hire ratio
Actives YoY % Change  -- Headcount trajectory
```

**Skills Demonstrated:** Star schema design, time intelligence (YoY/SPLY), demographic segmentation, Power BI report navigation, custom tooltip pages.

---

### 2. 💼 Wealth & Banking Manager View — Tableau

**File:** `Tableau/Wealth_and_Banking/wealth_and_banking_Dashboard.twbx`

An advisor performance and client onboarding pipeline dashboard for a wealth management team of 25 advisors. Built in Tableau with LOD expressions, dynamic parameters, and rolling period logic.

| Attribute | Detail |
|---|---|
| **Tool** | Tableau (.twbx) |
| **Domain** | Wealth Management / Financial Services |
| **Parameters** | Advisor Name, Client Name, Rolling Period |
| **Key Metrics** | GWP/Asset Value, Policies, Clients, Time in Pipeline, NILR |

**Dashboard Sections:**

| Section | Focus |
|---|---|
| GWP / Asset Value | Portfolio value with period variance and advisor rank (top 5 highlighted) |
| Policies | Asset/policy count vs. prior period with volume trend |
| Clients | Client base size and growth — current vs. prior 8-week window |
| Time in Pipeline | Total and average onboarding days; advisors ranked ascending (faster = better) |
| NILR — Avg Stage Time | Stage-level duration breakdown; colour flags stages deteriorating vs. prior period |
| SLA Outstanding | Active cases at risk of SLA breach |
| Overdue / Chase Flag | Items in stage >10 days requiring immediate advisor action |
| Top 10 Clients | Highest-value clients with current pipeline stage and value bar |
| Underwriters | Underwriter-level load distribution |

**Key Calculated Fields:**
```
[Days Until OOS]          -- Rolling OOS date forecast
[Avg Daily Loss]          -- Consumption rate over look-back window
[No of clients filter]    -- Advisor parameter-aware client count
[average stage time LOD]  -- FIXED/INCLUDE LOD for stage benchmarking
[SLA outstanding]         -- Compliance breach flag logic
```

**Skills Demonstrated:** Parameter actions, LOD expressions (FIXED/INCLUDE), rolling period calculations, advisor-level drilldown, league table ranking, light/dark mode navigation.

---

### 3. 📦 Inventory Management Dashboard — Tableau

**File:** `Tableau/Inventory/Inventory.twbx`

A multi-warehouse inventory health and out-of-stock risk dashboard covering three product categories — Furniture, Office Supplies, and Technology. Includes a forward-looking Gantt planning view projecting when items will deplete based on consumption rate.

| Attribute | Detail |
|---|---|
| **Tool** | Tableau (.twbx) |
| **Domain** | Supply Chain / Inventory Management |
| **Dashboards** | Overview (Light & Dark), Planning (Light & Dark) |
| **Parameters** | Detail View, Go Back X Days, Max Date |

**Stock Status Classification:**

| Status | Trigger |
|---|---|
| ✅ Within Range | `Target Stock Min ≤ Qty ≤ Target Stock Max` |
| 🟡 Over-Stock | `Qty > Target Stock Max` |
| 🔴 Under-Stock | `0 < Qty < Target Stock Min` |
| ⚫ Out-of-Stock | `Qty = 0` |

**Overview Dashboard:**
- Three parallel category columns: Furniture, Office Supplies, Technology
- Per-category: callout KPI, status distribution bar, monthly trend chart, Top 5 movers table
- Detail switcher toggles all views across: Positions, Quantity, Value, Missing Stock Cost, Surplus Cost

**Planning Dashboard:**
- Gantt chart forecasting days until OOS per item–warehouse combination
- Metrics: Avg Daily Loss, Avg Purchase, Days Until OOS, Expected OOS Date, Estimated Cost ($)
- Look-back window configurable (default 120 days) — shorter windows capture recent demand shifts

**Financial KPIs:**
```
Missing Stock Cost  =  (Target Stock Min - Qty in Stock) × Unit Value
Surplus Stock Cost  =  (Qty in Stock - Target Stock Max) × Unit Value
```

**Skills Demonstrated:** Multi-dashboard architecture, light/dark mode variants, Gantt chart construction, LOD-based cost calculations, warehouse-level data availability handling, parameter-driven detail switching.

---

## 🛠️ Tools & Technologies

| Tool | Version / Notes |
|---|---|
| Power BI Desktop | .pbix format, star schema, DAX |
| Tableau Desktop | .twbx format, Hyper extract, LOD expressions |
| DAX | Time intelligence, YoY, SPLY, ratio metrics |
| Tableau Calculated Fields | FIXED/INCLUDE LOD, parameter actions, rolling dates |
| Data Modeling | Star schema, denormalized extracts |

---

## 🚀 Getting Started

### Power BI

1. Install [Power BI Desktop](https://powerbi.microsoft.com/desktop/) (free)
2. Open the `.pbix` file directly
3. Data is embedded — no external connection required

### Tableau

1. Install [Tableau Desktop](https://www.tableau.com/products/desktop) or use [Tableau Public](https://public.tableau.com/) (free)
2. Open the `.twbx` file — the Hyper data extract is bundled
3. Use the parameter controls to explore different views and advisor selections

---

## 📐 Design Principles

All dashboards in this portfolio follow consistent standards:

- **Executive-first layout** — KPI banners at top, supporting detail below
- **Period-over-period comparison** — every headline metric is benchmarked against a prior period
- **Parameterised interactivity** — single controls that update all views simultaneously
- **Status-coded colour** — consistent Red / Amber / Green / Black classification across all visuals
- **Warehouse/data availability handling** — explicit "No Data" messaging rather than blank visuals

---

## 📄 License

This repository is for portfolio and demonstration purposes. Datasets used are sample/synthetic data. All dashboard designs are original work by Edwin Kato.

---

*Built with Power BI · Tableau · DAX · Tableau LOD Expressions*
