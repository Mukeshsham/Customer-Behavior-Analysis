# Customer-Behavior-Analysis
Customer Behavior Analysis project using Python and SQL to analyze customer demographics, engagement, product reviews, and purchase journeys for valuable business insights.
This repository contains a mini project for analyzing customer behavior using Python and SQL. The project involves loading customer data, performing sentiment analysis, visualizing trends, and querying a database to extract insights about customer actions, product reviews, and campaign effectiveness.

Table of Contents
Overview

Features

Technologies Used

Setup Instructions

Python Code

SQL Queries

Database Schema

Visualization

License

Overview
The Customer Behavior Analysis project aims to understand customer behavior patterns by analyzing datasets such as customer demographics, engagement data, product reviews, and purchase journeys. The project uses Python for data processing and visualization, and SQL for querying structured data stored in a MySQL database.

Features
Python Features:
Load and preprocess customer data.

Identify missing values.

Perform sentiment analysis on customer reviews using TextBlob.

Visualize age distribution of customers who made purchases.

SQL Features:
Identify stages where customers drop off during their journey.

Analyze the performance of ad campaigns.

Detect products with poor ratings.

Find customers with the highest number of purchases.

Technologies Used
Python: Data processing, sentiment analysis, and visualization.

Libraries: pandas, numpy, matplotlib, TextBlob

MySQL: Database for storing and querying structured data.

SQL: Writing queries to analyze customer behavior.

Setup Instructions
Prerequisites
Install Python (version >= 3.8).

Install MySQL (version >= 8.0).

Install required Python libraries:

bash
pip install pandas numpy matplotlib textblob mysql-connector-python
Steps
Clone the repository:

bash
git clone https://github.com/yourusername/customer-behavior-analysis.git
cd customer-behavior-analysis
Set up the MySQL database using the provided schema in database_schema.sql.

Place the CSV files (Customers.csv, Customer_journey.csv, engagement_data.csv, Customer_reviews.csv) in the project directory.

Run the Python script (analysis.py) to analyze data and visualize trends.

Python Code
Data Loading and Preprocessing
python
import pandas as pd
import numpy as np

# Load datasets into DataFrames
try:
    customers = pd.read_csv('Customers.csv')
    journey = pd.read_csv('Customer_journey.csv')
    engagement = pd.read_csv('engagement_data.csv')
    reviews = pd.read_csv('Customer_reviews.csv')

    # Preview data structure
    print(customers.head())
    print(journey.head())
    print(engagement.head())
    print(reviews.head())

except FileNotFoundError as e:
    print(f"Error: {e}. Please check if the file exists in the specified path.")
Sentiment Analysis on Reviews
python
from textblob import TextBlob

# Analyze sentiment of review text
reviews['Sentiment'] = reviews['ReviewText'].apply(lambda x: TextBlob(x).sentiment.polarity)

# Find negative reviews (sentiment < 0)
negative_reviews = reviews[reviews['Sentiment'] < 0]
print(negative_reviews[['ProductID', 'ReviewText']].head())
Visualization
python
import matplotlib.pyplot as plt

# Plot age distribution of customers who made purchases
purchases = journey[journey['Action'] == 'Purchase']
merged_data = pd.merge(purchases, customers, on='CustomerID')

plt.hist(merged_data['Age'], bins=10)
plt.xlabel('Age')
plt.ylabel('Number of Purchases')
plt.title('Age Distribution of Purchasers')
plt.show()
SQL Queries
1. Where are customers leaving?
sql
SELECT 
    Stage,
    COUNT(*) AS Visitors,
    SUM(CASE WHEN Action = 'Purchase' THEN 1 ELSE 0 END) AS Purchases
FROM CustomerJourney
GROUP BY Stage;
2. Which ads are making money?
sql
SELECT 
    e.CampaignID,
    SUM(e.ViewsClicksCombined) AS Engagement,
    COUNT(j.JourneyID) AS Purchases
FROM EngagementData e
LEFT JOIN CustomerJourney j ON e.UserID = j.CustomerID
GROUP BY e.CampaignID;
3. What products do people hate?
sql
SELECT 
    p.ProductID,
    p.ProductName,
    AVG(r.Rating) AS AvgRating,
    COUNT(r.ReviewID) AS ReviewCount
FROM SportsProducts p
JOIN ReviewData r ON p.ProductID = r.ProductID
GROUP BY p.ProductID
HAVING AvgRating < 3.5;
4. Find customers with the most purchases:
sql
SELECT 
    CustomerID,
    COUNT(*) AS TotalPurchases
FROM CustomerJourney
WHERE Action = 'Purchase'
GROUP BY CustomerID
ORDER BY TotalPurchases DESC;
Database Schema
The database consists of the following tables:

SportsProducts: Stores product details like ID, name, category, and price.

GeographyData: Contains location data (country and city).

EngagementData: Tracks user engagement with campaigns.

ReviewData: Stores product reviews with ratings and text.

CustomerJourney: Tracks customer actions across different stages.

Refer to the database_schema.sql file for detailed table structures.

Visualization
The project includes visualizations such as:

Histogram showing the age distribution of customers who made purchases.

Insights from sentiment analysis on customer reviews.

License
This project is licensed under the MIT License - see the LICENSE file for details.

Feel free to contribute or raise issues if you encounter any problems! 😊
