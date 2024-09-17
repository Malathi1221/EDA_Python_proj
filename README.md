# EDA_Python_proj-description
-Performed EDA on real-world online retail data to uncover sales trends and customer behavior patterns, describing data to answer key questions and uncover actionable analytic insights that enhance retail performance.
-Utilized Python for data cleaning, processing, and visualization, identifying best-selling products and valuable customers.
-Provided data-driven recommendations for strategic decisions, enhancing store performance and boosting sales.

# Project Objectives-EDA
Describe data to answer key questions to uncover insights
Gain valuable insights that will help improve online retail performance
Provide analytic insights and data-driven recommendations

# STEP-1: Loading the Data
  import pandas as pd
  #Load the dataset
  df = pd.read_excel("Online Retail.xlsx")
  #Display basic information about the dataset
  print("Data Information:")
  print(df.info())
  #Display the first few rows of the dataset
  print("\nFirst 5 Rows:")
  print(df.head())

# STEP-2: Explore the Data    
  import matplotlib.pyplot as plt
  import seaborn as sns
  #Summary statistics
  print("\nSummary Statistics:")
  print(df.describe())
  import pandas as pd
  
  import seaborn as sns
  import matplotlib.pyplot as plt
# Assuming your DataFrame is named 'df'
df['InvoiceDate'] = pd.to_datetime(df['InvoiceDate']) # Convert InvoiceDate to datetime
df['Month'] = df['InvoiceDate'].dt.month
df['DayOfWeek'] = df['InvoiceDate'].dt.dayofweek  # Monday=0, Sunday=6  # Extract Month and DayOfWeek

# Set a custom color palette (e.g., company colors)
custom_palette = ['#007acc', '#ff7f0e', '#2ca02c', '#d62728', '#9467bd', '#8c564b', '#e377c2']

# Grouping data by Month and DayOfWeek
monthly_sales = df.groupby('Month')['Quantity'].sum().reset_index()
dayofweek_sales = df.groupby('DayOfWeek')['Quantity'].sum().reset_index()

# Plot monthly sales with a line chart
plt.figure(figsize=(10, 5))
sns.lineplot(x='Month', y='Quantity', data=df, ci=None, color=custom_palette[0])
plt.xlabel('Month')
plt.ylabel('Total Sales')
plt.title('Monthly Sales Trends')
plt.xticks(range(1, 13), ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec'])
plt.grid(axis='y', linestyle='--', alpha=0.7)
plt.tight_layout()
plt.show()

# Plot day of the week sales with a bar chart
plt.figure(figsize=(8, 4))
sns.barplot(x='DayOfWeek', y='Quantity', data=df, ci=None, palette=custom_palette)
plt.xlabel('Day of the Week')
plt.ylabel('Total Sales')
plt.title('Day of the Week Sales Trends')
plt.xticks(range(7), ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun'])
plt.grid(axis='y', linestyle='--', alpha=0.7)
plt.tight_layout()
plt.show() 

# STEP-3: Cleaning and Validating the Data                                                                                                                                                       # data-cleaning: Handle missing values
print("\nMissing Values:")
print(df.isnull().sum())

# Drop rows with missing CustomerID
df_cleaned = df.dropna(subset=['CustomerID'])

# Remove duplicate rows
df_cleaned = df_cleaned.drop_duplicates()

# Handle negative quantities
df_cleaned = df_cleaned[df_cleaned['Quantity'] > 0]

# Handle outliers in UnitPrice
Q1 = df_cleaned['UnitPrice'].quantile(0.25)
Q3 = df_cleaned['UnitPrice'].quantile(0.75)
IQR = Q3 - Q1
df_cleaned = df_cleaned[(df_cleaned['UnitPrice'] >= (Q1 - 1.5 * IQR)) & (df_cleaned['UnitPrice'] <= (Q3 + 1.5 * IQR))]

# Validate cleaned data
print("\nCleaned Data Information:")
df_cleaned.info()

#fixing structural errors
# Rename columns
df.rename(columns={"Description": "ProductDescription"}, inplace=True)

# Convert InvoiceDate to datetime format
df["InvoiceDate"] = pd.to_datetime(df["InvoiceDate"]) 

# Re-Check for missing values in the CustomerID column
missing_customer_ids = df['CustomerID'].isnull().sum()
print(f"Number of missing CustomerID values: {missing_customer_ids}") 

# STEP-4: Analyzing the Data
Once the data has been cleaned and validated and appears to be in good shape, we can continue to analyze the data further.
Be sure to consider the main questions you were looking to answer when scoping out the project. A few examples of what you may want to consider analyzing and visualizing price per neighborhood or price per room type (shared room, an entire place, etc.)
-Best Selling Products
-Most Valuable Customers
-Sales by Country
-Visualizations

import matplotlib.pyplot as plt
import seaborn as sns
import pandas as pd

# Best-selling products
best_selling_products = df_cleaned.groupby('Description')['Quantity'].sum().sort_values(ascending=False).head(10)
print("\nBest-Selling Products:")
print(best_selling_products)

# Most valuable customers
df_cleaned['TotalPrice'] = df_cleaned['Quantity'] * df_cleaned['UnitPrice']
most_valuable_customers = df_cleaned.groupby('CustomerID')['TotalPrice'].sum().sort_values(ascending=False).head(10)
print("\nMost Valuable Customers:")
print(most_valuable_customers)

# Sales by country
sales_by_country = df_cleaned.groupby('Country')['TotalPrice'].sum().sort_values(ascending=False)
print("\nSales by Country:")
print(sales_by_country)

#Visualizations
# Set the Seaborn style
sns.set(style="whitegrid")

# Top 10 Best-Selling Products
plt.figure(figsize=(10, 5))
sns.barplot(x=best_selling_products.values, y=best_selling_products.index, palette="viridis")
plt.title('Top 10 Best-Selling Products', fontsize=16)
plt.xlabel('Total Quantity Sold', fontsize=14)
plt.ylabel('Product Description', fontsize=14)
plt.show()

# Top 10 Most Valuable Customers
plt.figure(figsize=(10, 5))
sns.barplot(x=most_valuable_customers.values, y=most_valuable_customers.index, palette="plasma")
plt.title('Top 10 Most Valuable Customers', fontsize=16)
plt.xlabel('Total Revenue', fontsize=14)
plt.ylabel('Customer ID', fontsize=14)
plt.show()

# Sales by Country
plt.figure(figsize=(10, 5))
sns.barplot(x=sales_by_country.values, y=sales_by_country.index, palette="coolwarm")
plt.title('Sales by Country', fontsize=16)
plt.xlabel('Total Revenue', fontsize=14)
plt.ylabel('Country', fontsize=14)
plt.show()

# Sales per month
df_cleaned['InvoiceDate'] = pd.to_datetime(df_cleaned['InvoiceDate'])
df_cleaned['Month'] = df_cleaned['InvoiceDate'].dt.to_period('M').astype(str)  # Convert Period to string for plotting
monthly_sales = df_cleaned.groupby('Month')['TotalPrice'].sum().reset_index()

plt.figure(figsize=(10, 5))
sns.lineplot(data=monthly_sales, x='Month', y='TotalPrice', marker='o')
plt.title('Monthly Sales Trend', fontsize=16)
plt.xlabel('Month', fontsize=14)
plt.ylabel('Total Revenue', fontsize=14)
plt.xticks(range(1, 13), ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec'])
plt.grid(axis='y', linestyle='--', alpha=0.7) 
plt.xticks(rotation=45)
plt.show() 

# STEP-5: Findings and Conclusions
Finally, we can wrap up the project. You can write a conclusion about your process and any key findings.
The main components that you will want to include: 
● What did you learn throughout the process? 
● Are the results what you expected? 
● What are the key findings and takeaways? 

# Summary of findings
print("Findings and Conclusions")
print("=========================")
print("1. The best-selling products are:")
print(best_selling_products)
print("\n2. The most valuable customers are:")
print(most_valuable_customers)
print("\n3. Sales distribution by country:")
print(sales_by_country)
print("\nKey takeaways:")
print("- The store has the highest sales in the UK.")
print("- Certain products are extremely popular and contribute significantly to sales.")
print("- Identifying and targeting the most valuable customers can enhance revenue.")
print("\nRecommendations:")
print("- Focus marketing efforts on the best-selling products.")
print("- Provide incentives or loyalty programs for the most valuable customers.")
print("- Explore opportunities in countries with lower sales to expand the market.") 
