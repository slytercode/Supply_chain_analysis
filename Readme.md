# Supply Chain Sales — Regional Trends & Returns (2014–2017)

**Stack**: SQLite (Python `sqlite3`) · Idempotent SQL · Tableau Public  
**Goal**: Reproducible pipeline from raw CSV to public dashboard, answering three business questions:
- **Monthly sales by state/region** (map)
- **Top category by region** (bars)
- **Return rate by category/sub-category** (heatmap)

---

##  Dashboard
 **Tableau Public**: [Open the visualization](https://public.tableau.com/app/profile/simone.piantoni/viz/Supply_chain_analysis_17620061426280/Completeviz)

*How to read it*: select a **Year**; keep **Month = All** for annual view or pick a single month for a monthly view.  
The map always shows all states; months with no sales are displayed as zero (no “missing states”).

---

## Business Cases
1) **Sales by State / Month**  
   *Question*: How do sales vary by state over time?  
   *Output*: filled map with Year/Month filters; color scale min fixed at 0 for comparability.

2) **Top Category by Region**  
   *Question*: Which product category dominates each region?  
   *Output*: one bar per region showing the highest-selling category (labels show category + sales).

3) **Return Rate by Category / Sub-Category**  
   *Question*: Where are returns concentrated?  
   *Output*: heatmap showing `return_rate` (bounded between 0–12/15%), with returned/total orders in tooltips.

---

## Key Insights (validated from the SQLite model)
- **Total sales (2014–2017)**: **2,297,201.07** (dataset currency units).  
- **Top 5 states by sales (2014–2017)**:
  - California: **457,687.68**
  - New York: **310,876.20**
  - Texas: **170,187.98**
  - Washington: **138,641.29**
  - Pennsylvania: **116,512.02**
- **Overall returns**: **800** returned orders out of **9,994** total → **~8.0%** return rate. Check product quality for tech (11%) 
- **Top category** : Technology in three Region

---

## Pipeline (end-to-end)

**Ingest → Clean → Model → BI views → Export → Tableau**

- **Step 1 — Ingest**: raw CSV → `stg_orders` (robust parsers for dates/decimals; essential indexes).  
- **Step 2 — Cleaning**: `Rsc_cleaned` (deduplicate on `row_id`; normalize text; `returned` → {YES, NO}; numeric guardrails).  
- **Step 3 — Modeling**: `dim_date`, `dim_geo`, `dim_product` (VIEWs) and `fct_sales` (TABLE with indexes).  
- **Step 4 — BI scaffold**: `viz_state_month_sales` at **State × Year × Month** grain, left-joined to facts with **zeros** for months without sales (prevents “missing states”).  
- **Step 5 — BI tables**: `viz_top_category_by_region` (one row per region) and `viz_returns_by_cat_subcat` (counts + rate).  
- **Step 6 — Export** (only what Tableau needs):
