# Sales Dashboard – Power BI Project

An interactive Power BI dashboard that analyses two years of grocery and food-product sales. It tracks revenue, orders, and average order value, and breaks performance down by time, region (US state), category, and product.

> 📸 **Dashboard preview:** add a screenshot here, for example `![Dashboard](images/dashboard.png)`

---

## 📌 Project Overview

| Item | Detail |
|---|---|
| **Goal** | Give a clear, interactive view of sales performance and trends |
| **Tool** | Microsoft Power BI Desktop (Power Query, DAX, data modelling) |
| **Data period** | 29 Jul 2023 – 28 Jul 2025 |
| **Records** | 1,000 orders |
| **Source table** | `Data_Cleaning` (cleaned in Power Query) |

---

## 🎯 Business Questions Answered

1. How much revenue are we generating, and how is it trending over time?
2. How do sales this period compare with the same period last year?
3. Which regions (states) and product categories perform best?
4. Which products are the top sellers?
5. What is the average order value and average units per order?

---

## 🗂️ Dataset

The model uses a single cleaned fact table, **`Data_Cleaning`**.

| Column | Type | Description |
|---|---|---|
| OrderID | Whole number | Unique order identifier |
| OrderDate | Date | Date the order was placed |
| CustomerName | Text | Customer who placed the order |
| Product Name | Text | Product sold |
| Category | Text | Product category (21 categories, e.g. Alcoholic Beverages, Vegetables, Meat) |
| Quantity | Whole number | Units sold |
| Price | Decimal | Unit price |
| Total Sale | Decimal | Quantity × Price |
| Region | Text | US state of the sale (46 states) |

**Data summary**
- 1,000 orders, 830 distinct products, 21 categories, 46 regions
- Total sales ≈ **3.21 million**, total quantity ≈ **25,202 units**
- Average order value ≈ **3,210**

---

## 🧱 Data Model

```
          ┌──────────────┐
          │     Date     │  (marked as Date table)
          │  Date (PK)   │
          └──────▲───────┘
                 │ 1
                 │
                 │ *
          ┌──────┴───────┐
          │Data_Cleaning │  (fact table)
          │ OrderDate FK │
          └──────────────┘
```

- **Date table**: created in DAX, covering Jan 1 of the first year to Dec 31 of the last year of the data. It includes Year, Quarter, Month, Month Number, Year-Month, Weekday, and Weekday Number.
- **Relationship**: `Data_Cleaning[OrderDate]` → `Date[Date]` (many-to-one, single direction).
- **Sort orders**: `Month` sorts by `Month Number`, and `Weekday` by `Weekday Number`.

### Data-preparation steps
- Removed trailing spaces from the column names (`OrderID`, `OrderDate`, `CustomerName`, `Product Name`).
- Set correct data types for dates, numbers, and text.
- Built a dedicated Date table to support time-intelligence calculations.

---

## 📐 DAX Measures

### Sales Metrics
| Measure | DAX |
|---|---|
| Total Sales | `SUM(Data_Cleaning[Total Sale])` |
| Total Quantity | `SUM(Data_Cleaning[Quantity])` |
| Total Orders | `DISTINCTCOUNT(Data_Cleaning[OrderID])` |
| Average Order Value | `DIVIDE([Total Sales], [Total Orders])` |
| Avg Units per Order | `DIVIDE([Total Quantity], [Total Orders])` |

### Time Intelligence
| Measure | Purpose |
|---|---|
| Sales YTD / QTD / MTD | Running totals for the year, quarter, and month |
| Sales Last Year | Same period in the previous year |
| Sales YoY Change / Sales YoY % | Year-over-year difference and percentage |
| Sales Previous Month / Sales MoM % | Month-over-month comparison |

```DAX
Sales Last Year = CALCULATE([Total Sales], SAMEPERIODLASTYEAR('Date'[Date]))
Sales YoY %     = DIVIDE([Sales YoY Change], [Sales Last Year])
```

### Share & Ranking
| Measure | Purpose |
|---|---|
| Sales % of Total | Share of the current selection |
| Region % of Sales | Each region's share of total sales |
| Category % of Sales | Each category's share of total sales |
| Product Rank | Ranks products by sales (used for Top N) |
| Total Customers | Distinct customer count |

### Trend
| Measure | Purpose |
|---|---|
| Average Monthly Sales | Flat reference line for the monthly average |
| 3M Moving Avg | Smoothed 3-month moving average of monthly sales |

```DAX
3M Moving Avg =
VAR _Last3Months = DATESINPERIOD('Date'[Date], MAX('Date'[Date]), -3, MONTH)
RETURN DIVIDE(CALCULATE([Total Sales], _Last3Months), 3)
```

---

## 📊 Dashboard Layout

| Section | Visual | Fields |
|---|---|---|
| Header | Title banner | – |
| Filters | 3 slicers | Year, Region, Category |
| KPIs | 5 cards | Total Sales, Total Orders, Total Quantity, Average Order Value, Sales YoY % |
| Trend | Line chart | Year-Month vs Total Sales, Sales Last Year, 3M Moving Avg, plus a trend line |
| Regional | Bar chart | Region by Total Sales |
| Category | Donut chart | Category share of Total Sales |
| Products | Top 10 bar chart | Product Name by Total Sales (Top N filter) |
| Customers | Table | CustomerName, Total Orders, Total Sales (with data bars) |

**Colour palette:** navy `#1F3A5F`, teal `#2A9D8F`, amber `#E9C46A`, orange `#F4A261`, red-orange `#E76F51`.

---

## 🔍 Key Insights

- **Top categories by sales:** Alcoholic Beverages (≈ 439.7K), Vegetables (≈ 366.7K), and Meat (≈ 266.2K).
- **Top regions by sales:** Texas (≈ 339.0K), California (≈ 318.2K), and Florida (≈ 260.3K).
- Sales are spread across many states and categories, so no single region or category dominates the business.

> Update this section with your own observations after exploring the final dashboard, such as seasonality, growth trends, and low performers.

---

## 🛠️ Tools & Skills Demonstrated

- Power BI Desktop: report design and interactivity
- Power Query: data cleaning and type handling
- Data modelling: star-schema approach with a dedicated Date table
- DAX: aggregations, time intelligence, ranking, and moving averages
- Data visualisation and dashboard design

---

## 📁 Repository Structure

```
Project_1/
├── Sales_Dashboard.pbix      # Power BI report (rename to match your file)
├── Model_Backup/             # Semantic model exported as TMDL (version-control friendly)
│   ├── Data_Cleaning.tmdl
│   ├── Date.tmdl
│   ├── relationships.tmdl
│   └── ...
├── images/                   # Dashboard screenshots
└── README.md
```

---

## ▶️ How to Use

1. Clone or download this repository.
2. Open the `.pbix` file in **Power BI Desktop**.
3. Use the slicers (Year, Region, Category) to filter every visual on the page.
4. Hover over visuals for tooltips, and click chart elements to cross-filter.

> If you move the project, update the data source path under **Home → Transform data → Data source settings**.

---

## 👤 Author

**Your Name**
[LinkedIn](https://www.linkedin.com/) · [GitHub](https://github.com/)
