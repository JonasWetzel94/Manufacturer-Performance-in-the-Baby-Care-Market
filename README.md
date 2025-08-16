# Manufacturer Performance in the Baby Care Market — Pivot-based analytics from raw FMCG data


## Case Description
Build a management-ready Excel report that analyzes **manufacturers, brands, package types/sizes, SKUs, and product attributes** across **2022, 2023, and YTD-2024**. The business wants a **single, slicer-driven** view with clear YoY trends and the ability to drill down from manufacturer to item level. :contentReference[oaicite:0]{index=0}

---

## Tasks
- Clean messy headers (merged year/measure labels) and restructure into a **flat table** suitable for pivots. :contentReference[oaicite:1]{index=1}  
- Add **Manufacturer** and **Brand** columns and populate them programmatically (no manual typing). :contentReference[oaicite:2]{index=2}  
- Remove **brand/manufacturer subtotal rows** to avoid double counting. :contentReference[oaicite:3]{index=3}  
- Build a **primary pivot** (Manufacturer × Year × {Value, Volume}) and connect it to a formatted report grid via **GETPIVOTDATA** with error handling. :contentReference[oaicite:4]{index=4}  
- Add **slicers** (Brand, Package, Attributes, Size) and a small **interpretation** panel with YoY metrics. :contentReference[oaicite:5]{index=5}  

---

## Accounting/Analytics Steps
1. **Flatten header & remove noise**
   - Drop the top title row; ensure a **single header row** with explicit fields:  
     `Product Description, Manufacturer, Brand, Package, Product Attributes, Size, Value_2022, Value_2023, Value_2024YTD, Volume_2022, Volume_2023, Volume_2024YTD`. :contentReference[oaicite:6]{index=6}
2. **Manufacturer & Brand enrichment**
   - Filter blue MANUFACTURER/BRAND rows; use **Fill Right** then **IF** to fill down the manufacturer per row:  
     `=IF(B3<>"", B3, C2)` → convert to values.  
   - Extract Brand vs. Manufacturer via **Text-to-Columns** (split on parentheses) and map with **XLOOKUP**. :contentReference[oaicite:7]{index=7}
3. **Subtotal removal**
   - Filter for “brand” markers; delete subtotal rows to prevent double counting in pivots. :contentReference[oaicite:8]{index=8}
4. **Pivot + report grid**
   - Pivot fields: **Rows**=Manufacturer, **Columns**=Year, **Values**={Sum of Value, Sum of Volume}.  
   - Link the formatted report grid with **GETPIVOTDATA** and wrap with **IFERROR** to hide #REF/#DIV/0 from missing combinations. :contentReference[oaicite:9]{index=9}
5. **KPIs & conditional formatting**
   - Compute YoY: `=IFERROR(Current/Prior-1,0)`; highlight outliers (e.g., price deviations). Freeze panes for usability. :contentReference[oaicite:10]{index=10}

---

## Trial Balance / Data Summary (table + totals/checks)
*Units: Sales in **$m** (original data in $ thousands); Volume in **m units** (original in thousands).*

| Metric                    | 2022 | 2023 | 2024 YTD | Check |
|---|---:|---:|---:|---|
| **Sales ($m)**            | 11.880 | 12.265 | 6.590 | Source totals = pivot totals ✅ |
| **Volume (m units)**      | 470.248 | 475.047 | 239.949 | Source totals = pivot totals ✅ |
| **Avg Price ($/unit)**    | 0.0253 | 0.0258 | — | Price = Sales/Volume ✅ |
| **YoY 23 vs 22 — Sales**  | **+3.24%** | — | — | Reconciles to pivot ✅ |
| **YoY 23 vs 22 — Volume** | **+1.02%** | — | — | Reconciles to pivot ✅ |
| **YoY 23 vs 22 — Price**  | **+2.20%** | — | — | Sales ≈ Vol × Price ✅ |

Additional dataset facts: **107 manufacturers**, **159 brands**, **551 items** (post-cleaning).

---

## Financial Statements / Results (Model Outputs)
**Executive highlights (2023 vs 2022):**
- **Sales** +3.24% driven by **Price** +2.20% and **Volume** +1.02% (mature market dynamics).  
- **Top 3 manufacturers by YoY sales growth** (filter: >1,000,000 units in 2023):
  - **ErusHealth Products**: +761.9% revenue; **price −18.7%**, volume surge to 13.6m units.  
  - **MoreBeauty**: +212.3% revenue; **price +15.1%**, 4.6m units.  
  - **SigmaKappaZeta Co.**: +175.5% revenue; **price −17.8%**, 3.1m units.  
- **Product attributes** (2023 vs 2022):  
  - **Alcohol Free**: Sales **+21.2%**, Volume **+20.4%**, Price **+0.6%**.  
  - **PH Balanced**: Sales **−1.7%**, Volume **−6.5%**, Price **+5.2%**.  
  - **Sensitive**: Sales **+13.8%**, Volume **+27.8%**, Price **−11.0%**.  
  - **Without Extra Protectcare**: Sales **−6.0%**, Volume **−7.5%**, Price **+1.6%**.  
*(All results computed from the attached source workbook.)*

---

## Mapping / Logic
**From Source → Clean Table → Pivot → Report:**
- **Row typing** infers context: when `row_type="MANUFACTURER"` set current manufacturer; when `row_type="BRAND"` set current brand; assign both to subsequent `ITEM` rows; ignore subtotal rows.
- **Measures**:  
  - `Sales (Value)` and `Volume` tracked for each year (2022, 2023, YTD-2024).  
  - `Avg Price = Value / Volume`.  
  - `YoY% = (Current / Prior) − 1`.
- **Report binding** (example):  
  ```excel
  =IFERROR(
    GETPIVOTDATA("Value",$B$3,"Manufacturer",$A10,"Year",2023),
  0)


## How I Built It (Tools & Techniques)
- Excel: Power cleaning (filters, Fill Right/Down), Text-to-Columns, XLOOKUP, GETPIVOTDATA, IFERROR, Pivot Tables, Slicers, Conditional Formatting, Freeze Panes. 
- Data rules: keep only ITEM granularity; drop brand/manufacturer subtotals; standardize columns; ensure numeric dtypes for all measures.
- Robustness: all report cells reference pivot with GETPIVOTDATA (no hard links); errors hidden via IFERROR; totals validated against source sums.
