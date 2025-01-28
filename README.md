# Retail Analytics-Project

Description:
Analyzing customer and transaction data from a large retailer of apparel and accessories with locations across the U.S. The company is interested in analyzing both profitability and customer satisfaction. For the current phase of the project, they've asked you to focus on their four store locations in and around the Boston metro area.



- Data modeling.
Create an entity relationship diagram to show how the customers, sales, and stores data files are related to each other. Your diagram should also identify all relevant primary and foreign keys for each entity.

- Inspection and cleaning.
Inspect the customers file and identify the column(s), if any, that have problematic categorical data. Clean the categorical data to ensure it conforms to the requirements. *Hint—your data should only include “acceptable values” as listed in the description column of the data dictionary. Note that missing values in the survey question fields are acceptable. 
Inspect the customers file and identify the column(s), if any, that have problematic numerical data. Clean the numerical data to address data errors, missing values, and outlier values.

- Data manipulation and wrangling.
Using the sales file, create a table that shows the number of items purchased and average item sale price for each customer.id that appears in the sales data.
Join the customer-level transaction data with the cleaned version of your customers data to create a 'customer_purchases' dataframe. Save the data in .csv or .xlsx format.

- Summary statistics and visualization.
Compute the mean, median, standard deviation, and skewness coefficient for sale amount, using the data in the 'sales' file.
Produce well-formatted boxplots to show the distribution of sale amount (a) for all sales records in the data, and (b) separately for each product category.
Calculate the blended gross margin for each product category, i.e., the gross margin percentage earned across all purchases within that category. Blended gross margin is calculated by applying the formula (sale.amount - ext.cost)/sale.amount at the category level, i.e., use the sum of sale.amount and sum of ext.cost (across all observations in the category) in this formula.
Using the entire 'sales' data file, identify any outliers in the 'sale.amount' variable using either the boxplot method or the z-score method. Recommend a method for handling the outliers based on your assessment of their causes and frequency.
 

Part 2:

 Statistical Inference
 

The retailer is currently facing several key challenges that they hope to better understand and address with analytics. This portion of the analysis is intended to address the following issues:

 Business performance, as measured by gross margin percentage (GM%) and gross margin dollars (GM$), has been inconsistent.
When GM% is low relative to the company's average, it indicates that items were discounted more than usual (likely because they were slow to sell).
There appears to be variation in performance across stores and across product categories, making it hard to determine the root causes of low GM%.
Note: GM$ reflects both GM% and volume; a store of category might have high GM% but low GM$ or vice versa. GM% is the firm’s primary performance metric, but it's important to keep in mind the size (in GM$) of each store and category when making recommendations.
Forecasting future GM$ has been a challenge because of seasonality and other variation in the data.
The retailer knows that business is seasonal, but the underlying trends in financial performance and the degree of variation across stores in the seasonal patterns is not well understood.

5. Hypothesis testing.

Identify a hypothesis about a population mean or population proportion that can be tested with data and that relates to some subset of the following variables:
store
category
gross.margin
price.category
sale.amount
seasonal factors (e.g., month or week of the year). If you'd like help formulating a relevant hypothesis, please attend office hours.

Use the appropriate statistical tests to test this hypothesis using the data and present the results in a clear format with a concise explanation of what the decision means in business terms.

6. Regression analysis. 

Build a regression model to predict gross.margin using any other data that is available to you. Because of the high inherent variability in the outcome measure, achieving an R2 of ~0.15 or higher is considered useful to the firm. However, the model should also provide interpretable results, e.g., information about which variables most affect gross.margin in a positive or negative direction.
