# 📊 Global Crude Oil Price Analytics (1970–2026)
### End-to-End Power BI Business Intelligence Project

![Dashboard Overview](assets/crude_dashboard_1.png)

---

## 🗂️ Project Overview

A complete end-to-end **Data Warehousing & Business Intelligence** project analyzing **56 years of monthly Brent crude oil prices** across 676 data points (January 1970 – March 2026).

Built as part of the DWBI course at the **University of Illinois Urbana-Champaign (UIUC)**, this project demonstrates a full production-grade BI workflow: 
from raw CSV extraction through Power Query transformation, DAX semantic modeling, and interactive dashboard design in Power BI Desktop.

The four dashboards are designed so that **any viewer, regardless of energy market knowledge can immediately understand what happened, when, and why** just by reading the visuals.

---

## 📁 Repository Structure

```
crude-oil-analytics/
├── Crude_Oil_Analytics.pbix        ← Full Power BI project file (open in Power BI Desktop)
├── fuel_prices_1970_2026.csv       ← Raw source dataset (Kaggle)
├── README.md
└── assets/
    ├── crude_dashboard_1.png       ← Dashboard 1: Price History (1970–2026)
    ├── crude_dashboard_2.png       ← Dashboard 2: Volatility & Shocks
    ├── crude_dashboard_3.png       ← Dashboard 3: Decade DNA
    └── crude_dashboard_4.png       ← Dashboard 4: Seasonal Pulse
```

---

## 🔧 How to Open

1. Download and install **[Power BI Desktop](https://powerbi.microsoft.com/desktop/)** (free)
2. Clone or download this repository
3. Open `Crude_Oil_Analytics.pbix` in Power BI Desktop
4. All data, transformations, measures, and dashboards load automatically — no setup required

---

## 🏗️ ETL Pipeline (Power Query)

Raw data was a simple 2-column CSV: `Date` and `Crude_Oil_Price`. The following transformations were applied in Power Query before any analysis:

| Step | Transformation | Output Column |
|---|---|---|
| 1 | Set data types | `Date` → Date, `Crude_Oil_Price` → Decimal |
| 2 | Round price | `Price_USD` (2 decimal places) |
| 3 | Extract date parts | `Year`, `Month`, `Month Name`, `Quarter` |
| 4 | Create quarter label | `Quarter_Label` (e.g. "Q3 2008") |
| 5 | Derive decade | `Decade` (e.g. "2000s") |
| 6 | Classify price era | `Price_Era` (Ultra-Low / Low / Moderate / High / Extreme) |
| 7 | Flag crisis periods | `Crisis_Period` (9 named historical events) |
| 8 | Sort Month Name | By numeric Month column |

A separate `DateTable` was created in DAX and related to `FuelPrices[Date]` to enable all time-intelligence functions.

---

## 📐 DAX Measures (15 total)

```dax
-- Core aggregations
Avg Price         = AVERAGE(FuelPrices[Price_USD])
Max Price         = MAX(FuelPrices[Price_USD])
Min Price         = MIN(FuelPrices[Price_USD])
All Time Avg      = CALCULATE(AVERAGE(FuelPrices[Price_USD]), ALL(FuelPrices))
All Time High     = CALCULATE(MAX(FuelPrices[Price_USD]), ALL(FuelPrices))
All Time Low      = CALCULATE(MIN(FuelPrices[Price_USD]), ALL(FuelPrices))
Latest Price      = CALCULATE(LASTNONBLANK(FuelPrices[Price_USD],1), ALL(FuelPrices))
Price Count       = COUNT(FuelPrices[Price_USD])

-- Rolling averages (time intelligence)
Rolling 12M Avg   = CALCULATE(AVERAGE(...), DATESINPERIOD(..., -12, MONTH))
Rolling 3M Avg    = CALCULATE(AVERAGE(...), DATESINPERIOD(..., -3, MONTH))

-- Change metrics
YoY Price Change %  = DIVIDE(CurrentYear - PriorYear, PriorYear) * 100
MoM Price Change %  = DIVIDE(CurrentMonth - PrevMonth, PrevMonth) * 100
Abs YoY Change      = ABS([YoY Price Change %])

-- Statistical spread
P25 Price         = PERCENTILE.INC(FuelPrices[Price_USD], 0.25)
P75 Price         = PERCENTILE.INC(FuelPrices[Price_USD], 0.75)
Price StdDev      = STDEV.P(FuelPrices[Price_USD])
Seasonal Index    = DIVIDE([Avg Price], [All Time Avg])
```

---

## 📊 The Four Dashboards

### Dashboard 1 — Price History (1970–2026)
![Dashboard 1](assets/crude_dashboard_1.png)

The full cinematic view of crude oil history designed so anyone can understand it without prior knowledge.

**Key visuals:**
- 4 KPI cards: All-Time High ($132.83), All-Time Low ($1.21), Latest Price, 55-Year Avg ($39.80)
- Dual-line chart: Actual monthly price + 12-month rolling average (1970–2026)
- 3 horizontal price threshold lines: Moderate ($60), High ($90), Extreme ($110)
- 4 annotated crisis events: 1973 Oil Embargo, 2008 Financial Crisis, 2020 COVID Crash, 2022 Ukraine War
- Interactive slicers: Filter by Decade (tile) and Filter by Price Era (dropdown)

---

### Dashboard 2 — Volatility & Shocks
![Dashboard 2](assets/crude_dashboard_2.png)

Reveals the emotional drama of oil markets — not just price levels, but how violently they moved.

**Key visuals:**
- Color-coded YoY % change column chart (blue = rise, red = crash) with annotations on extreme years
- Rolling 3M vs 12M vs actual price convergence chart
- Avg YoY % change by decade bar chart (replaces donut — more analytically meaningful)
- Heat-colored shock magnitude table sortable by severity
- Cross-filtering slicers: Crisis Period + Decade

**Key insight:** 1974 recorded a +211% YoY spike — the most extreme single-year move in 56 years of data.

---

### Dashboard 3 — Decade DNA
![Dashboard 3](assets/crude_dashboard_3.png)

Each decade had a completely different personality. This dashboard reveals it.

**Key visuals:**
- Horizontal bar chart: Average price by decade with intentional warm-to-red color progression
- Clustered column chart: Min / Avg / Max price spread per decade
- Scatter plot: Price level vs. price volatility per decade — reveals 2000s/2010s as uniquely high-price AND high-volatility
- Conditional-formatted scorecard matrix: All key stats per decade in one glance
- Best/worst months table with heat-colored price cells

**Key insight:** The 1990s had *lower volatility* than the 1970s despite similar average prices — stability came from different geopolitical conditions, not lower prices.

---

### Dashboard 4 — Seasonal Pulse
![Dashboard 4](assets/crude_dashboard_4.png)

Does crude oil follow a seasonal rhythm? This dashboard answers it definitively.

**Key visuals:**
- 56 × 12 matrix heatmap (Year × Month) with blue-to-red gradient — the entire post-2000 price regime shift is visible at a glance
- Average price by calendar month line chart: July/June historically peak, January lowest
- Month-over-Month change chart: Q1 (Jan/Feb) is consistently the weakest seasonal period
- Decade-breakout clustered chart: Average monthly price by decade — shows the seasonal pattern has remained structurally stable even as absolute price levels tripled

---

## 🗃️ Data Source

- **Dataset:** [Global Fuel Price Trends 1970–2026](https://www.kaggle.com/datasets/minahilfatima12328/global-fuel-price-trends19702026) — Kaggle
- **Coverage:** January 1970 – March 2026 (676 monthly records)
- **Commodity:** Brent crude oil spot price (USD per barrel)

---

## 🛠️ Tech Stack

| Tool | Purpose |
|---|---|
| **Power BI Desktop** | Dashboard design, visual layer, interactivity |
| **Power Query (M)** | ETL: extraction, cleaning, transformation, loading |
| **DAX** | Semantic model: 15 measures including time-intelligence |
| **Star Schema** | Data model: FuelPrices fact table + DateTable dimension |

---

## 👤 Author

**Tejas Jaggi** — MS Information Management, UIUC  
[Portfolio](https://tejas-jaggi.github.io) · [LinkedIn](https://www.linkedin.com/in/tejas-jaggi-a55134221) · [GitHub](https://github.com/tejas-jaggi)

---

## 📄 License

This project is open for reference and portfolio use. Dataset credit: Minahil Fatima via Kaggle (CC0).
