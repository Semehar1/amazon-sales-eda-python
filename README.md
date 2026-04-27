# 📊 Amazon Sales Analysis (EDA with Python)

## 📌 Project Overview
This project performs an in-depth Exploratory Data Analysis (EDA) on Amazon product data to uncover key patterns in pricing, customer engagement, and product performance.

The objective is to transform raw e-commerce data into actionable insights that could support data-driven decision-making in a retail environment.

---

## 🎯 Objectives
- Identify top-performing products and categories  
- Analyze the relationship between price, ratings, and demand  
- Estimate product-level revenue using available proxies  
- Extract insights relevant to business and marketing strategy  

---

## 🗂️ Dataset Description
The dataset contains approximately 1,400 Amazon products with features such as:
- Product name and category  
- Actual and discounted prices  
- Discount percentage  
- Customer ratings and review counts  
- Product descriptions and metadata  

---

## ⚙️ Methodology

### 1. Data Cleaning
- Removed non-numeric characters from price columns  
- Converted data types for analysis  
- Handled missing values  
- Removed inconsistent records  

### 2. Feature Engineering
Estimated Revenue = Rating Count × Discounted Price  

This proxy is used to approximate product performance in the absence of actual sales data.

### 3. Exploratory Data Analysis
- Distribution of product prices and ratings  
- Top products by estimated revenue  
- Category-level revenue contribution  
- Visualizations using Matplotlib and Seaborn  

---

## 📊 Key Insights
- A small number of products generate a large share of total estimated revenue  
- High ratings do not necessarily correlate with high sales performance  
- Mid- to low-priced products dominate demand  
- Revenue distribution varies significantly across categories  

---

## 💡 Business Recommendations
- Focus marketing efforts on high-performing product segments  
- Optimize pricing strategies to balance demand and profitability  
- Avoid relying solely on ratings as a success metric  
- Identify underperforming categories for improvement  

---

## 🛠️ Tools & Technologies
- Python  
- Pandas  
- NumPy  
- Matplotlib  
- Seaborn  

---

## 📁 Project Structure
amazon-sales-eda-python/
│
├── data/  
├── notebooks/  
├── images/  
├── README.md  

---

## 🚀 Future Improvements
- Correlation and regression analysis  
- Predictive modeling  
- Interactive dashboard (Power BI / Streamlit)  
- Time-series analysis  

---

## 📌 Key Takeaway
This project demonstrates the ability to:
- Work with real-world datasets  
- Perform structured data analysis  
- Translate data into business insights  
