# Sales Analysis: TheLook eCommerce
## Tools Used
 * **Data Source**: Google BigQuery (`thelook_ecommerce`)
 * **Data Transformation**: SQL (BigQuery)
 * **Analysis & Visualization**: Power BI 


## Business Objectives & Analytical Questions
-----------------------------------------------------------
### I. Profitability & Inventory
 * **Objective**: Identify the most profitable product categories.
 * **Business Question**: Which product categories generate the highest margin (difference between sale price and cost)?
 * **Key Metrics**: Total Profit, Profit Margin per Category.

![Dashboard](Image/Executive_Profitability_Overview-TheLook_eCommerce.png)

### Key Business Insights:
 * **Operational Profit Leaders**: Outerwear & Coats and Jeans drive the highest Total Profit, even though their percentage margins are not the highest. This indicates high sales volume and their critical role in the company's revenue stream.

 * **High-Margin "Stars"**: The Blazers & Jackets category achieves the peak profit margin (nearly 65%). While its total profit is moderate, it shows great potential for scaling sales while maintaining high unit profitability.

 * **Low-Profitability Segments**: Categories like Socks, Suits, and Clothing Sets exhibit both the lowest margins (below 40%) and low total profit. These areas may require a procurement cost review or a pricing strategy overhaul.

 * **Growth Opportunities in Maternity & Accessories**: These categories maintain healthy margins above 55-60%, but their contribution to total profit remains low. Increasing marketing efforts for these groups could significantly boost overall financial performance.

-----------------------------------------------------------
### II. Marketing Effectiveness (Customer Acquisition)
* **Objective**: Evaluate customer lifetime value based on acquisition channels.
* **Business Question**: Which traffic source brings in customers with the highest Average Order Value (AOV)?
* **Key Metrics**: Average Order Value (AOV), User Count per Source.

![Dashboard](Image/Marketing_Attribution.png)

### Key Business Insights:

 * **Primary Acquisition Drivers**: In terms of volume, Search is the clear global leader in customer acquisition, followed closely by Organic traffic. This highlights that search engine visibility (both paid and SEO) is the fundamental pillar of the company’s customer base growth.
 * **Male Demographic Dominance (LTV)**: Customer Lifetime Value (LTV) is significantly higher for men across all marketing channels, consistently exceeding $100, compared to approximately $85-$90 for women. This suggests a strategic opportunity to optimize product offerings or marketing campaigns tailored to female customers.
 * **Email Channel Efficiency**: While Search brings in the highest volume of users, Email yields the highest Average Order Value (AOV), especially in the 45-60+ age groups, making it the most effective channel for maximizing individual basket size.
 * **Traffic Quality Stability**: Despite the variance in volume between channels like Search and Facebook, the AOV remains remarkably stable (approx. $78–$82). This indicates a consistent acquisition strategy that attracts customers with similar purchasing power across all platforms.
-----------------------------------------------------------
### III. Operational Optimization (Returns Analysis)
* **Objective**: Reduce losses resulting from product returns.
* **Business Question**: Which brands or product categories exhibit the highest return rates?
* **Key Metrics**: Return Rate (%), Total Returns.

![Dashboard](Image/Product_Return_Insights&Trends.png)

### Key Business Insights:
  * **Return Rate Stability**: The analysis reveals a remarkably consistent return rate across all product groups, mostly oscillating between 9.5% and 10.5%. This suggests that return drivers are systemic across the entire inventory rather than tied to specific category defects.

  * **Sales-Return Correlation**: There is a high correlation between sales volume and return count. This indicates that the rising number of returns is a direct result of increased sales scale, rather than a decline in product quality.

  * **High-Risk Categories**: Blazers, Jackets, and Outerwear show slightly higher return probabilities (above 10.5%), likely due to more complex sizing requirements for structured garments.

  * **Low-Return Segments**: The Maternity and Plus categories exhibit the lowest return rates, suggesting accurate sizing charts or higher purchase intent in these specialized segments.
-----------------------------------------------------------
