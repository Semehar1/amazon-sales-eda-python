 Amazon Sales Analysis (EDA with Python)
📌 Overview
This project explores Amazon product data to uncover patterns in sales performance, pricing, and customer ratings. The goal is to move beyond basic visualization and extract actionable business insights that could inform decision-making in an e-commerce environment.

🎯 Objectives


Analyze product performance across categories


Identify factors influencing estimated revenue


Explore relationships between price, ratings, and demand


Derive insights relevant to business strategy



🗂️ Dataset
The dataset contains information on Amazon products, including:


Product name and category


Price and discounted price


Ratings and number of reviews



⚙️ Methodology
1. Data Cleaning


Handled missing values


Converted price columns to numeric format


Removed inconsistencies in the dataset


2. Feature Engineering


Created an estimated revenue metric:
Revenue ≈ Rating Count × Discounted Price


This serves as a proxy to approximate product performance


3. Exploratory Data Analysis (EDA)


Distribution of product prices and ratings


Top-performing products and categories


Revenue concentration analysis


Visualizations using Matplotlib and Seaborn



📊 Key Insights


A small subset of products contributes disproportionately to total estimated revenue


High product ratings do not always translate into high sales volume


Lower- to mid-priced products tend to dominate in terms of demand


Significant variation exists across product categories in both pricing and performance



💡 Business Implications


Focus marketing efforts on high-performing product segments


Pricing strategy plays a critical role in driving volume


Ratings alone are not sufficient indicators of sales success


Opportunity to optimize product positioning based on demand patterns



🛠️ Tools & Technologies


Python


Pandas


NumPy


Matplotlib


Seaborn



🚀 Future Improvements


Perform correlation and regression analysis


Build a predictive model for sales estimation


Develop an interactive dashboard (Power BI / Streamlit)


Incorporate time-series analysis if temporal data is available



📎 Project Structure
├── data/├── notebooks/├── images/├── README.md

📬 Contact
Feel free to connect or provide feedback—always open to improving and learning.

⭐ Key Takeaway
This project demonstrates the ability to:


Work with real-world datasets


Apply structured data analysis techniques


Translate data findings into business insight
