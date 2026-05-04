# 📊 ShopEasy Marketing Analytics — Power BI Dashboard

> **shopeasy** · *Your effortless market*

An interactive Power BI dashboard built on the **ShopEasy Marketing Analytics** dataset.  
The report analyzes customer behavior, product performance, and marketing campaign effectiveness across **engagement, conversion, and satisfaction** dimensions.

---

## 📁 Data Source

- **Database:** `PortfolioProject_MarketingAnalytics` (SQL Server)
- **Extraction Tool:** SQL Server Management Studio (SSMS)
- **Tables:** `customers`, `products`, `customer_journey`, `customer_reviews`, `engagement_data`, `geography`

---

## 🗄️ Data Model

The project follows a **Galaxy Schema** design with `customer_journey`, `customer_reviews` and `engagement_data` as the two central fact tables, sharing common dimensions (`customers`, `products`, `geography`, `CalendarTable`).

***![DataModel](https://github.com/yousofahmed2074/ShopEasy-Marketing-Analytics-Power-BI-dashboard/blob/main/PICs/DataModel.PNG)***

### Tables

| Table | Key Column | Description |
|-------|-----------|-------------|
| `customer_journey` | `JourneyID` | Fact table — funnel stages, actions, and session duration per customer/product |
| `engagement_data` | `EngagementID` | Fact table — views, clicks, likes, and content type per campaign/product |
| `customer_reviews` | `ReviewID` | Review scores and sentiment-analyzed text per customer/product |
| `customers` | `CustomerID` | Customer demographics: name, gender, age, and geography |
| `products` | `ProductID` | Product name, category, and price |
| `geography` | `GeographyID` | Country and city dimension |
| `CalendarTable` | `Date` | Custom date dimension with Month, Quarter, and Year |

---

## 🧹 Data Cleaning

Performed in **Python** and **Power Query** before loading to the model:

### 1. Customer Journey
- **Removed duplicate rows** across the full table.
- **Standardized repeated values** — normalized inconsistent casing all resolved to a single clean value.

### 2. Customer Reviews — Sentiment Analysis
Review text was analyzed using Python's **VADER sentiment model** to produce a numeric sentiment score alongside the original star rating:

```python
from vaderSentiment.vaderSentiment import SentimentIntensityAnalyzer

analyzer = SentimentIntensityAnalyzer()
dataset['Sentiment_Score'] = dataset['ReviewText'].apply(
    lambda x: analyzer.polarity_scores(str(x))['compound']
)
dataset['Sentiment_Label'] = dataset['Sentiment_Score'].apply(
    lambda x: 'Positive' if x >= 0.05 else ('Negative' if x <= -0.05 else 'Neutral')
)
```

This produced two new columns: `Sentiment_Score` (float) and `Sentiment_Label` (Positive / Neutral / Negative).

### 3. Engagement Data
- **Standardized repeated values**
- **Split merged columns**

### 4. customers
- Merge **Geography** into **Customers** to transition from a Snowflake to a Galaxy Schema. 

---
## 📐 DAX Measures

Measures are organized into folders inside the `_Measures` table:

| Folder | Measures |
|--------|---------|
| **Engagement** | Engagement Rate, Total Views, Total Clicks, Total Likes, Total Visits |
| **Conversion** | Conversion Rate (Purchase/Visit), Conversion Rate (CheckOut), Total Purchases, Total Checkout, Total Journey Views |
| **Revenue** | AOV (Average Order Value) |
| **Reviews** | Avg Feedback Score, Avg Sentiment Score, Total Reviews |

---

## 📊 Dashboard

### 1. Main Keys
***![MainKeys](https://github.com/yousofahmed2074/ShopEasy-Marketing-Analytics-Power-BI-dashboard/blob/main/PICs/1%20MainKeys%20Dashboard.PNG)***

Key KPIs at a glance: **Engagement Rate (24.22%)**, Total Visits, Total Views (9.1M), Total Clicks (1.79M), and Total Likes (414K).  
Includes monthly trend lines for Avg Feedback Score and Engagement Rate, a campaign-level breakdown table, and a Totals by Month area chart.

---

### 2. Products
***![Products](https://github.com/yousofahmed2074/ShopEasy-Marketing-Analytics-Power-BI-dashboard/blob/main/PICs/2%20Products%20Dashboard.PNG)***

Product-level view covering **Conversion Rate (5.04%)**, AOV ($219.01), Total Checkout (774), and Total Purchases (198).  
Visualizes ConvRate per product (bar chart), Avg Feedback vs. Avg Sentiment Score per product (line chart), Purchase Per Product (treemap), and Total Checkout Per Product (bar chart).

---

### 3. Customer
***![Customer](https://github.com/yousofahmed2074/ShopEasy-Marketing-Analytics-Power-BI-dashboard/blob/main/PICs/3%20Customer%20Dashboard.PNG)***

Customer-centric view with **Conversion Rate by Country** (bar chart), **Customers by Country** (bar chart), and a detailed customer-level table showing ConvRate, Total Purchases, Total Visits, Total Checkout, and Checkout ConvRate.

---

## 🔍 Project Insights

- **Engagement Rate is 24.22%** across 9.1M views and 3,932 visits — engagement is strong but the overall purchase conversion rate is low at **5.04%**, indicating friction between browsing and buying.
- **Engagement peaks in January (~29%)** then steadily declines through December (~18%), suggesting front-loaded campaign performance that fades without refreshed content.
- **Avg Feedback Score trends downward** across products, while Avg Sentiment Score follows a similar path — both signal that customer satisfaction is slightly eroding for higher-ID (likely newer) products.
- **Netherlands and Germany lead in conversion rate** (6.10% and 6.03%), while Sweden lags at 3.01% — geographic targeting could significantly improve overall conversion.
- **Spain has the highest customer count (18)** despite not topping conversion rates, suggesting a large but less-engaged audience that needs nurturing.
- **Product 19 leads in conversion rate (7.54%)** and purchase volume — it represents the strongest performer and a benchmark for other products.
- **AOV is $219.01** across only 198 total purchases — a relatively small purchase volume with high average value, meaning each lost conversion is costly.
- **Campaign 7 drives the most total clicks (92,347)** with a 23.27% engagement rate, making it the highest-volume campaign by clicks despite not having the highest engagement rate.

---

## 💡 Final Conclusion

ShopEasy's marketing data reveals a platform with **strong top-of-funnel performance** — high views, clicks, and engagement rates — but a significant drop-off before purchase, with only a 5.04% conversion rate across 3,932 visits and 198 total purchases.

The data points to geographic opportunities (Netherlands and Germany converting well), product-level differentiation (Product 19 as a clear leader), and a sentiment gap in newer products that warrants attention.

Closing the gap between engagement and conversion — through better retargeting, checkout optimization, and campaign refreshes — represents the clearest growth lever available.

---

## 🎯 Key Takeaways for Business

| # | Takeaway |
|---|----------|
| 1 | **Reduce funnel drop-off** — 24.22% engagement rate vs. 5.04% conversion rate is a major gap; invest in retargeting and checkout UX improvements. |
| 2 | **Double down on Netherlands & Germany** — these markets convert at 6%+; allocate more campaign budget here for the highest ROI. |
| 3 | **Re-engage Spain** — largest customer base but mid-tier conversion; a nurturing email or loyalty campaign could unlock significant volume. |
| 4 | **Study Product 19** — the top converter at 7.54%; understand what drives its performance and replicate across the catalog. |
| 5 | **Address declining sentiment scores** — newer products trend lower on both feedback and sentiment; investigate quality, expectations, or description accuracy. |
| 6 | **Refresh campaigns mid-year** — engagement drops steadily after January; a mid-year campaign burst could recover the Q3–Q4 decline. |

---

## 🛠️ Tools Used

- **Power BI Desktop** — Report building, DAX, Power Query
- **SQL Server Management Studio (SSMS)** — Data extraction
- **Power Query (M)** — Data transformation and Cleaning
- **Python (pandas + vaderSentiment)** — Sentiment analysis on review text
- **DAX** — Measures and KPI calculations
- **Figma** — Dashboard UI design

---

## 🚀 How to Use

1. Restore or connect to the `PortfolioProject_MarketingAnalytics` SQL Server database.
2. Open the `.pbix` file in **Power BI Desktop**.
3. Update the data source connection to point to your SQL Server instance.
4. Refresh the data model to load the latest records.
