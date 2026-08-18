# 🛒 Amazon Sales Data — Cleaning & Analysis

### Data Cleaning • Feature Engineering • Exploratory Data Analysis • Business Insights

**Created by Rahma Ashraf Ali Faragallah**

---

## 📌 Project Overview

This project focuses on cleaning, transforming, analyzing, and visualizing an **Amazon sales dataset** to uncover meaningful patterns related to product pricing, discounts, customer reviews, estimated sales, brands, sponsored listings, and coupons.

The project starts with a highly unstructured dataset where all columns were stored as text and several fields contained mixed formats, missing values, duplicated records, and embedded information.

The analysis transforms this raw dataset into a structured and analysis-ready dataset, followed by exploratory visualizations and business-oriented insights.

---

## 🎯 Project Objectives

The main objectives of this project are to:

* Understand the structure and quality of the raw dataset.
* Identify and remove duplicate records.
* Handle missing values appropriately.
* Convert text-based numerical fields into usable numeric formats.
* Extract meaningful information from complex text columns.
* Engineer new analytical features.
* Explore product price distributions.
* Analyze the relationship between discounts and customer reviews.
* Identify the top-performing brands based on estimated units sold.
* Examine correlations between important numerical variables.
* Compare sponsored and organic product listings.
* Investigate the relationship between coupons and estimated sales.
* Translate analytical findings into actionable business insights.

---

## 📊 Dataset Overview

| Attribute        |                          Description |
| ---------------- | -----------------------------------: |
| Original Rows    |                               42,675 |
| Original Columns |                                   16 |
| Duplicate Rows   |                                  962 |
| Data Type Issue  | All columns initially stored as text |
| Domain           |      E-Commerce / Amazon Marketplace |
| Analysis Type    |            Exploratory Data Analysis |

The dataset contains information related to:

* Product titles
* Ratings
* Number of reviews
* Current/discounted prices
* Listed prices
* Variant prices
* Estimated purchases
* Brand information
* Sponsored listings
* Best Seller / Amazon's Choice badges
* Coupons
* Sustainability badges
* Buy-box availability
* Collection timestamps

---

# 🧹 Data Cleaning

The raw dataset required several cleaning operations before analysis.

## 1. Duplicate Removal

A total of **962 fully duplicated rows** were identified and removed.

```python
df = df.drop_duplicates()
```

This prevents duplicated products from artificially affecting statistical analysis and visualizations.

---

## 2. Rating Cleaning

The original rating values were stored as text such as:

```text
4.6 out of 5 stars
```

The numerical rating was extracted using regular expressions and converted to `float`.

```python
df['Rating'] = df['Rating'].str.extract(r'(\d+\.\d+)').astype(float)
```

Missing ratings were intentionally preserved as `NaN` rather than being imputed or removed.

---

## 3. Review Count Cleaning

Review counts originally contained thousands separators, for example:

```text
2,457
```

The commas and unnecessary spaces were removed before converting the values to numeric.

---

## 4. Estimated Monthly Purchases

The `bought_in_last_month` column contained values such as:

```text
300+ bought in past month
6K+ bought in past month
```

The numeric component and `K` suffix were extracted and converted into a numerical feature:

```text
bought_last_month_num
```

For example:

```text
6K → 6000
300+ → 300
```

---

## 5. Price Cleaning

Several price-related columns required transformation:

* `Current/discounted_price`
* `Price_on_variant`
* `Listed_price`

Currency symbols and thousands separators were removed and the values were converted to numeric types.

For products marked as:

```text
No Discount
```

the listed price was set equal to the current price.

This allows the discount percentage to correctly become:

```text
0%
```

rather than being treated as missing.

---

## 6. Badge Feature Engineering

The original badge column contained multiple meanings within one field.

Separate boolean features were created for:

* Best Seller
* Amazon's Choice
* Limited Time Deal
* Ends-in Deal

A separate feature was also created to capture percentage discounts associated with Best Seller-related badges.

---

## 7. Coupon Feature Engineering

The original coupon column contained different types of promotions, including:

```text
Save 15% with coupon
Save $16.00 with coupon
No Coupon
```

The column was transformed into multiple analytical features:

* `has_coupon`
* `coupon_discount_pct`
* `coupon_discount_amount`

This makes coupon behavior easier to analyze.

---

## 8. Sustainability Badges

The sustainability badge column was highly sparse.

Instead of creating multiple sparse columns for different badge types, a single boolean feature was created:

```text
has_sustainability_badge
```

---

## 9. Buy Box Availability

Since the original buy-box column contained essentially one meaningful non-null state (`Add to cart`), it was transformed into:

```text
is_available_to_buy
```

---

# ⚙️ Feature Engineering

Additional features were created to make the dataset more useful for analysis.

### Brand Extraction

A simplified brand feature was extracted from the first word of the product title:

```python
df['Brand'] = df['Title'].str.split().str[0]
```

> **Note:** This is a simplified approach and may truncate multi-word brand names such as "Texas Instruments."

### Collection Date

The `collected_at` column was converted from text into a proper datetime format:

```python
df['collected_at'] = pd.to_datetime(
    df['collected_at'],
    format='%d-%m-%Y %H:%M'
)
```

### Discount Percentage

A new feature was created to calculate the percentage discount:

```python
df['discount_percentage'] = (
    (df['Listed_price'] - df['Current/discounted_price'])
    / df['Listed_price']
) * 100
```

---

# 📈 Exploratory Data Analysis

The project contains **six major visual analyses**.

## 1. Product Price Distribution

### Visualization

Histogram showing the distribution of current/discounted product prices.

### Key Insight

The price distribution is strongly **right-skewed**, with most products concentrated at lower price levels and a smaller number of expensive products creating a long upper tail.

### Business Interpretation

Because extreme prices can influence the mean, the **median price** can provide a more representative measure of the typical product price.

---

## 2. Discount % vs. Number of Reviews

### Visualization

Scatter plot comparing discount percentage with number of reviews.

### Key Insight

The relationship between discount percentage and review count is extremely weak, indicating that discount size alone does not explain customer engagement.

### Business Interpretation

Customer engagement is likely influenced by multiple factors, including:

* Brand
* Product popularity
* Product category
* Advertising
* Price
* Product quality
* Customer experience

Therefore, aggressive discounting should not automatically be assumed to generate more reviews or purchases.

---

## 3. Top 10 Brands by Estimated Units Sold

### Visualization

Horizontal bar chart ranking the top 10 brands by estimated units sold during the previous month.

### Key Insight

Estimated sales are highly concentrated among a small number of leading brands, with **Duracell and Energizer** standing out significantly from the rest of the analyzed brands.

### Business Interpretation

Brand recognition and market position may play an important role in sales volume.

This also suggests an opportunity for deeper analysis of the leading brands to identify differences in:

* Pricing
* Reviews
* Ratings
* Product visibility
* Sponsorship
* Promotions

---

## 4. Correlation Heatmap

### Variables Analyzed

* Rating
* Number of Reviews
* Current/Discounted Price
* Estimated Units Sold
* Discount Percentage

### Key Findings

| Relationship               | Correlation |
| -------------------------- | ----------: |
| Reviews ↔ Estimated Sales  |    **0.30** |
| Rating ↔ Estimated Sales   |    **0.12** |
| Price ↔ Estimated Sales    |   **-0.09** |
| Discount ↔ Estimated Sales |    **0.04** |
| Discount ↔ Reviews         |    **0.02** |

### Interpretation

The strongest relationship among the analyzed variables is between **review count and estimated sales**, although a correlation of `0.30` is still not strong enough to treat reviews as a standalone predictor of sales.

The other relationships are relatively weak.

> **Important:** Correlation does not imply causation.

---

## 5. Sponsored vs. Organic Listings

### Visualization

Box plots comparing:

* Product prices
* Product ratings

between sponsored and organic listings.

### Key Insight

Sponsored and organic listings show broadly overlapping rating distributions, suggesting that sponsored placement should not be interpreted as a direct indicator of product quality.

The price distributions also show substantial overlap, although their central tendencies may differ slightly.

### Business Interpretation

Advertising placement and product quality should be treated as separate dimensions.

A sponsored product is primarily a **paid visibility decision**, not necessarily a quality signal.

---

## 6. Coupon Effect on Estimated Sales

### Visualization

Bar chart comparing average estimated units sold for products:

* With coupons
* Without coupons

### Key Insight

Products without coupons show a higher average estimated sales volume than products with coupons in this dataset.

### Important Interpretation

This does **not** mean that coupons reduce sales.

A possible explanation is **reverse causality**:

> Products with weaker demand may be more likely to receive coupons in an attempt to stimulate sales.

A controlled experiment or time-based analysis would be required to determine the actual causal impact of coupons.

---

# 💡 Key Business Insights

The analysis leads to several important conclusions:

### 1. Price Distribution Is Highly Skewed

Most products are concentrated at lower price levels, while a smaller premium segment creates a long right tail.

### 2. Brand Concentration Matters

A small number of brands account for a large share of estimated unit sales.

### 3. Reviews Have Some Relationship with Sales

Review count has the strongest positive relationship with estimated sales among the tested variables, but the relationship is still moderate.

### 4. Discounts Alone Do Not Explain Engagement

Discount percentage shows almost no linear relationship with review count or estimated sales.

### 5. Sponsored Placement Is Not a Quality Indicator

Sponsored and organic products have broadly similar rating distributions.

### 6. Coupon Presence Is Not Proof of Higher Sales

Coupon products show lower average estimated sales in this dataset, but this observation cannot establish a causal relationship.

---

# 📌 Recommendations

Based on the analysis, the following steps could improve the business understanding of the marketplace:

### 1. Segment Products

Analyze products separately by:

* Category
* Price range
* Brand
* Product type

### 2. Analyze Leading Brands

Perform a deeper analysis of the highest-performing brands to understand what drives their sales advantage.

### 3. Evaluate Promotions More Carefully

Instead of relying only on correlation, compare product performance before and after promotions.

### 4. Add Time-Series Analysis

If multiple collection dates are available, track changes in:

* Price
* Discounts
* Coupons
* Estimated sales
* Reviews

over time.

### 5. Build a Predictive Model

A future machine learning phase could use:

* Price
* Rating
* Reviews
* Brand
* Discount
* Coupon status
* Sponsored status
* Product category

to predict estimated sales or classify high-performing products.

---

# 🛠️ Technologies & Libraries

The project was developed using Python and the following libraries:

| Technology           | Purpose                          |
| -------------------- | -------------------------------- |
| **Python**           | Main programming language        |
| **Pandas**           | Data manipulation and cleaning   |
| **NumPy**            | Numerical operations             |
| **Matplotlib**       | Data visualization               |
| **Seaborn**          | Statistical visualization        |
| **Jupyter Notebook** | Interactive analysis environment |

---

# 📂 Project Structure

```text
Amazon-Sales-Analysis/
│
├── Amazon_Sales_Project.ipynb
│
├── amazon_sales_data_uncleaned.csv
│
├── presentation/
│   └── Amazon_Sales_Analysis_Presentation.pptx
│
└── README.md
```

---

# 📊 Project Workflow

```text
Raw Dataset
     ↓
Data Inspection
     ↓
Data Cleaning
     ↓
Missing Value Analysis
     ↓
Duplicate Removal
     ↓
Data Type Conversion
     ↓
Text Extraction
     ↓
Feature Engineering
     ↓
Exploratory Data Analysis
     ↓
Visualization
     ↓
Business Insights
     ↓
Recommendations
```

---

# ⚠️ Limitations

Several limitations should be considered when interpreting the results:

* The dataset represents a snapshot rather than a complete time series.
* `bought_in_last_month` represents estimated purchase volume.
* Brand extraction uses the first word of the product title and may simplify multi-word brands.
* Correlation analysis measures linear association and does not establish causation.
* Coupon and promotion relationships may be affected by product selection and demand.
* Missing ratings were preserved rather than imputed.

---

# 🚀 Future Improvements

Potential extensions of this project include:

* Building an interactive **Power BI dashboard**.
* Performing category-level analysis.
* Performing deeper brand-level analysis.
* Creating a sales prediction model.
* Applying statistical hypothesis testing.
* Performing time-series analysis.
* Building an interactive Streamlit dashboard.
* Comparing product performance across different collection dates.
* Developing an ML model to identify high-performing products.

---

# 👩‍💻 Author

**Rahma Ashraf Ali Faragallah**

Data Analysis & AI Enthusiast

---

## ⭐ Project Highlights

**Data Cleaning | Feature Engineering | EDA | Data Visualization | Business Intelligence | Python**

If you find this project useful, feel free to ⭐ the repository.

---

> **“Clean data turns raw marketplace records into decisions — not just charts.”**

