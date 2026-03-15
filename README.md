# 🚗📊 MotoDB: Wholesale Vehicle Market Analytics

![Python](https://img.shields.io/badge/Python-3.8+-blue.svg)
![Pandas](https://img.shields.io/badge/Pandas-Data_Manipulation-150458.svg)
![Seaborn](https://img.shields.io/badge/Seaborn-Data_Visualization-4C72B0.svg)
![SQL](https://img.shields.io/badge/SQL-Database_Extraction-CC2927.svg)

## 📑 Table of Contents
1. [Project Overview](#project-overview)
2. [The Business Problem](#the-business-problem)
3. [Phase 1: Data Engineering Pipeline](#phase-1-data-engineering-pipeline)
4. [Phase 2: Exploratory Data Analysis (EDA)](#phase-2-exploratory-data-analysis-eda)
5. [Key Business Insights](#key-business-insights)
6. [How to Run](#how-to-run)

---

## 🌐 Project Overview
This project processes, sanitizes, and analyzes over **550,000 rows** of wholesale used vehicle auction data. The primary objective is to establish a robust Data Engineering pipeline that transforms raw, fragmented SQL database records into a mathematically clean dataset, enabling advanced Exploratory Data Analysis (EDA) to uncover market trends and vehicle depreciation curves.

## 💼 The Business Problem
In the wholesale automotive market, profit margins are won and lost on the auction block. Dealerships and fleet managers rely on the **MMR (Manheim Market Report)** as a baseline estimate for a vehicle's value. 

To determine true profitability, we engineered a custom Key Performance Indicator (KPI): 
> **`Market_Delta` = `SellingPrice` - `MMR`**

This project identifies which specific vehicle segments, brands, conditions, and geographic locations consistently outperform their MMR baseline, signaling prime acquisition targets.

---

## 🛠️ Phase 1: Data Engineering Pipeline
Raw auction data suffers from severe human-entry error. The pipeline executes the following programmatic sanitization steps to ensure statistical integrity:

* **Extraction:** Established an ODBC connection to extract the `VehicleSales` table directly from a local SQL Server instance into a Pandas DataFrame.
* **Feature Normalization:**
  * **Brand Consolidation:** Consolidated 96 highly fragmented variations of car makes (e.g., `ford`, `ford tk`, `Ford`) into 59 standardized corporate brand names using targeted dictionary mapping.
  * **Body Style Categorization:** Deployed a hierarchical keyword-extraction function to collapse 87 hyper-specific and messy variations (e.g., `CrewMax Cab`, `Koup`) into 8 primary market segments.
  * **Data-Shift Correction:** Identified and corrected CSV formatting bleed-over (e.g., "Sedan" appearing in the `Transmission` column).
* **Targeted Imputation:** * Utilized dictionary-based `.loc` mapping to hard-code body styles for high-volume ambiguous models (e.g., mapping "F150" to "Pickup").
  * Applied median imputation for continuous variables (`Odometer`, `ConditionValue`) to preserve distribution shapes.
* **The Purge:** Strictly dropped rows missing essential financial targets (`SellingPrice`, `MMR`). Imputing these target variables would poison the pricing model. 

**Result:** The dataset was reduced from 558,837 to a 100% clean **~546,900 rows**, fully optimized for Machine Learning and visualization.

---

## 📈 Phase 2: Exploratory Data Analysis (EDA)
With a fully sanitized dataset, we transitioned to visual analytics to extract actionable business intelligence.

### 1. Market Composition & Distribution (Univariate)
* **Inventory Dominance:** Sedans and SUVs make up the overwhelming majority of the wholesale auction volume.
* **Price Distribution:** Selling prices follow a classic right-skewed distribution, peaking heavily in the $10,000 - $20,000 range.

### 2. Value Drivers & Depreciation (Bivariate)
* **Mileage Penalty:** A scatter plot with a linear regression overlay confirmed a steep negative correlation (-0.58) between Odometer readings and Selling Price.
* **Condition Variance:** Box plots grouping condition scores (1-5) revealed that "Excellent" condition cars have a significantly wider pricing variance, indicating less predictable pricing at the top of the market.

### 3. The Executive KPI Dashboard (Multivariate)
* **Segment Profitability:** A diverging bar chart mapped the `Market_Delta`, revealing which specific body styles consistently sell above their MMR estimates.
* **Temporal Trends:** Time-series analysis resampling transaction dates weekly smoothed daily volatility, allowing us to observe macro market cooling/heating against expected estimates.

### 4. Niche Market Arbitrage (Deep Dives)
* **Geographic Arbitrage:** Mapped average profit/loss by State to identify geographic zones where demand outstrips supply.
* **Market Concentration:** Deployed a 2D Kernel Density Estimate (KDE) plot to map the true "center of gravity" of the market, revealing the densest trading cluster occurs between 20k-40k miles and $12k-$18k.

---

## 💡 Key Business Insights
1. **The Depreciation Curve is Aggressive Early:** Vehicles lose the vast majority of their auction value in the first 50,000 miles. Purchasing inventory just past this "depreciation cliff" offers the best baseline value.
2. **Commercial Vehicles Command Premiums:** Segments like Vans and Pickups consistently show a positive `Market_Delta`, indicating that commercial demand keeps prices higher than passenger-vehicle baselines.
3. **Location Matters:** Geographic arbitrage is a viable strategy; shipping specific vehicle types to high-demand states yields a higher profit margin than selling them locally, even after transportation costs.

---

## 💻 How to Run
1. Ensure you have Python 3.8+ and SQL Server installed.
2. Clone this repository.
3. Install required packages:
   ```bash
   pip install pandas matplotlib seaborn pyodbc
