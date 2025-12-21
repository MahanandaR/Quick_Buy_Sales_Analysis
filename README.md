# **Quick_Buy : Live Sales Dashboard**

<img width="1320" height="741" alt="image" src="https://github.com/user-attachments/assets/6bd8f853-7916-4861-95a1-7b05dd6c50aa" />

## Table of Contents

- [Problem Statement](#problem-statement)
- [Project Objective](#project-objective)
- [Setup](#setup)
- [ER Diagram](#er-diagram)
- [Database Schema](#database-schema)
- [SQL Views](#sql-views)
- [ETL Pipeline](#etl-pipeline)
- [GitHub Actions](#github-actions)
- [Power BI Dashboard](#power-bi-dashboard)
- [Results & Insights](#results--insights)
- [How To](#how-to)
- [Folder Structure](#folder-structure)
- [License](#license)

------------------------------------------------------------------------------
## Problem Statement

**Quick Buy** is a leading superstore operating across the United States, sells thousands of products across regions.

Currently, the store relies heavily on multiple spreadsheets and manual SQL queries to track business performance.

❖ This manual process makes it :

  - Hard to keep data structured and consistent.
  - Time-consuming to prepare reports.
  - Difficult to update daily transaction data manually.
  - Challenging for non-technical users to extract and understand key business insights.

❖ To solve these challenges, Quick Buy aims to build an automated system that :

  - Collects and stores all data in a structured database.
  - Cleans and updates data automatically using an ETL Pipeline.
  - Uses SQL to analyze data and extract meaningful business insights.
  - Displays insights through an interactive Power BI Dashboard.
  - Helps managers and executives quickly understand business performance and make better decisions.

-----------------------------------------------------------------------------

## Project Objective

- **Centralized Data Storage**
    - Store all business data such as orders, customers, and products in one SQL database.
    - Ensure data is well-structured, accurate and easy to access.
- **Automated ETL Pipeline**
    - Build an ETL (Extract, Transform, Load) pipeline using Python and SQLAlchemy.
    - Automatically load new data into the database every day.
    - Reduce manual reporting work and save time for the analytics.
- **Data Cleaning and Transformation**
    - Automatically clean raw data by fixing missing, duplicate or inconsistent values.
    - Ensure that data is clean and ready for the analysis.
- **Secure and Stable Database Connection**
    - Use a secure connection between Python and the database.
    - Store credentials safely using configuration files or environment variables.
    - Keep the system stable with low chances of connection failure.
- **SQL Views for Business Insights**
    - Create SQL views to simplify complex queries and calculations.
    - Use these views as a consistent data source for the dashboard.
- **Interactive Power BI Dashboard**
    - Build dashboard to visualize different aspects of the business like orders, customers, etc.
    - Design the dashboard to be clear and easy to understand for all users(technical and non-technical users).
- **Daily Report Refresh & Monitoring**
    - Refresh reports daily to show the latest data.
    - Use basic logging to track ETL runs and detect failures quickly.
-----------------------------------------------------------------------------------------------------
## Setup

### 1. Clone the Repository

  First, clone the project from GitHub to your local system using the command below:
  ```bash
  git clone https://github.com/MahanandaR/Quick_Buy_Sales_Analysis.git
  ```
### 2. Setup a Virtual Environment

  To keep project dependencies isolated and avoid version conflicts, create a virtual environment.
  
  On Windows:
  ```bash
  python -m venv .venv
  ```

On macOS/Linux :
```bash
python3 -m venv .venv
```

### 3. Activate the Virtual Environment
Once the virtual environment is created, activate it before installing any project dependencies.

On Windows:
```bash
.\.venv\Scripts\activate


On macOS/Linux :
```bash
source .venv/bin/activate
```
### 4. Install the Project Dependencies
Now, install all the required libraries inside your virtual environment using the `requirements.txt` file.
```bash
pip install -r requirements.txt
```

> [!TIP]
> It's a good idea to upgrade `pip` before installing dependencies to avoid compatibility issues.
```bash
pip install --upgrade pip
```

> [!NOTE]
> Use the same Python version as in `.github/workflows/etl_pipeline.yml` to avoid compatibility issues.

### 5. Setup Environment Variables
This project uses a `.env` file to store database credentials like `DB_USER`, `DB_PASS`, `DB_NAME`, etc.

It stores environment variables in plain text.
```python
# .env
DB_HOST=host_name
DB_NAME=database_name
DB_USER=user_name
DB_PASS=password
```

> [!IMPORTANT]
> Make sure not to commit your `.env` file to GitHub or any public repositories.
> 
> You can add it to `.gitignore` to ensure it's excluded from version control.

> [!NOTE]
> If you want to create a free Database in Neon and connect it with Python, go to [How To](#how-to) section.

### 6. Database Connectivity Check
To confirm that the PostgreSQL connection works before running ETL scripts.

This avoids script crashes due to invalid credentials or blocked ports.

```python
# Importing Libraries
from dotenv import load_dotenv
from sqlalchemy import create_engine

# Loading Environment File
load_dotenv()

# Loading Database Credentials from Environment File
DB_HOST = os.getenv("DB_HOST")
DB_NAME = os.getenv("DB_NAME")
DB_USER = os.getenv("DB_USER")
DB_PASS = os.getenv("DB_PASS")

# Creating SQLAlchemy Engine to upload the Data to Neon PostgreSQL Database
engine = create_engine(f"postgresql://{DB_USER}:{DB_PASS}@{DB_HOST}/{DB_NAME}?sslmode=require&channel_binding=require", pool_pre_ping=True)
```
### 7. Run ETL Script
This initializes the database and :
- Cleans raw CSV data
- Creates tables (`customers`, `orders`, `products`)
- Loads data into Neon PostgreSQL Database.
```bash
python scripts/etl.py
```

> [!NOTE]
> Run this only once initially or when you want a full database refresh.

### 8. Create SQL Views
This script builds reusable SQL views that summarize business metrics for Power BI Dashboard.

It simplifies queries, ensures consistent logic and improves performance.
```bash
python scripts/create_views.py
```

### 9. Generate New Data (Optional)
Simulates daily transactions by generating new random data for testing pipeline automation.

Helps verify how dashboards respond to new data over time.
```bash
python scripts/generate_data.py
```

### 10. Export Views as CSVs (Optional)
Exports SQL views results to CSV files inside the `views/` folder.

It is useful for sharing datasets or validating dashboard data without connecting to the database.
```bash
python scripts/export_views.py
```
### 11. Simulate Full Workflow Locally
Running all scripts manually helps confirm that everything works locally before enabling the scheduled GitHub run.
```bash
python scripts/etl.py
python scripts/create_views.py
python scripts/generate_data.py
python scripts/export_views.py
```

### 12. Check Logs
- Check log files inside `logs/` folder :
  - `etl.log` → Initial data loading.
  - `create_views.log` → SQL views creation.
  - `generate_data.log` → Daily data generation.
- Logs help you monitor pipeline performance and troubleshoot errors quickly.
-------------------------------------------------------------------------------------------
## ER Diagram

### Relationships
- **One Customer to Many Orders**
  - A single customer can place multiple orders over time.
- **One Product to Many Orders**
  - A single product can be included in multiple orders.
- **Orders as the Central Table**
  - The orders table acts as the main transactional table and connects customers with products.

<img width="700" height="500"  alt="image" src="https://github.com/user-attachments/assets/4f8c3d33-b365-412b-be43-c405c46d3570" />

-------------------------------------------------------------------------------------------------  
## Database Schema
The database is designed to store and organize **Quick Buy's** orders, customers and products data.

It ensures that all business data is centralized, consistent and easy to query for analysis and dashboarding.

### SQL Schema Definition 
```sql
/* Customers Table */
CREATE TABLE IF NOT EXISTS customers (
  customer_id TEXT PRIMARY KEY,
  customer_name TEXT,
  segment TEXT,
  city TEXT,
  state TEXT,
  country TEXT,
  postal_code NUMERIC,
  region TEXT
);
```

```sql
/* Products Table */
CREATE TABLE IF NOT EXISTS products (
  product_id TEXT PRIMARY KEY,
  product_name TEXT,
  category TEXT,
  sub_category TEXT
);
```

```sql
/* Orders Table */
CREATE TABLE IF NOT EXISTS orders (
  order_id TEXT PRIMARY KEY,
  order_date DATE,
  customer_id TEXT,
  product_id TEXT,
  ship_mode TEXT,
  ship_date DATE,
  sales NUMERIC,
  quantity INTEGER,
  discount NUMERIC,
  profit NUMERIC,
  shipping_duration INTEGER,
  profit_margin NUMERIC,
  FOREIGN KEY (customer_id) REFERENCES customers(customer_id),
  FOREIGN KEY (product_id) REFERENCES products(product_id)
);
```
--------------------------------------------------------------------------------------------------
## SQL Views
✦ SQL views act as a logical layer on top of raw tables to standardize business calculations.

✦ Instead of using complex queries repeatedly, Power BI connects directly to these views to retrieve clean and ready-to-use data.

<details>
  <summary><b>Click Here for detailed SQL Views</b></summary>
  <br>

  ```sql
  /* segment_wise_sales_and_profit */
  /* Calculates total sales and profit for each customer segment. */
  CREATE OR REPLACE VIEW segment_wise_sales_and_profit AS
  SELECT 
      c.segment,
      SUM(o.sales) AS total_sales,
      SUM(o.profit) AS total_profit
  FROM orders AS o
  JOIN customers AS c
  ON o.customer_id = c.customer_id
  GROUP BY c.segment;
  ```

  ```sql
  /* region_wise_sales_and_profit */
  /* Summarizes total sales and profit across all regions. */
  CREATE OR REPLACE VIEW region_wise_sales_and_profit AS
  SELECT 
      c.region,
      SUM(o.sales) AS total_sales,
      SUM(o.profit) AS total_profit
  FROM orders AS o
  JOIN customers AS c
  ON o.customer_id = c.customer_id
  GROUP BY c.region;
  ```

  ```sql
  /* month_wise_sales_and_profit */
  /* Shows monthly trends of total sales and profit. */
  CREATE OR REPLACE VIEW month_wise_sales_and_profit AS 
  SELECT
      TO_CHAR(order_date, 'Mon') AS month, 
      SUM(sales) AS total_sales,
      SUM(profit) AS total_profit
  FROM orders
  GROUP BY month;
  ```

  ```sql
  /* top_customers_by_sales */
  /* Lists customers with their total sales and profit to identify top performers. */
  CREATE OR REPLACE VIEW top_customers_by_sales AS
  SELECT
      c.customer_id, 
      c.customer_name,
      SUM(o.sales) AS total_sales,
      SUM(o.profit) AS total_profit
  FROM orders AS o
  JOIN customers AS c
  ON o.customer_id = c.customer_id
  GROUP BY c.customer_id, c.customer_name;
  ```

  ```sql
  /* shipping_performance */
  /* Analyzes sales and profit performance by shipping mode. */
  CREATE OR REPLACE VIEW shipping_performance AS 
  SELECT 
      ship_mode,
      SUM(sales) AS total_sales,
      SUM(profit) AS total_profit
  FROM orders
  GROUP BY ship_mode;
  ```

  ```sql
  /* overall_sales_performance */
  /* Provides overall business KPIs like total sales, profit, orders and customers. */
  CREATE OR REPLACE VIEW overall_sales_performance AS
  SELECT
      SUM(sales) AS total_sales,
      SUM(profit) AS total_profit,
      COUNT(DISTINCT order_id) AS total_orders,
      COUNT(DISTINCT customer_id) AS total_customers,
      COUNT(DISTINCT product_id) AS total_products,
      SUM(quantity) AS total_quantity_sold
  FROM orders;
  ```

  ```sql
  /* state_wise_sales_and_customer_base */
  /* Displays total sales and customer count by U.S. states. */
  CREATE OR REPLACE VIEW state_wise_sales_and_customer_base AS 
  SELECT
      c.state,
      SUM(o.sales) AS total_sales,
      COUNT(DISTINCT c.customer_id) AS total_customers
  FROM orders AS o
  JOIN customers AS c
  ON o.customer_id = c.customer_id
  GROUP BY c.state;
  ````

  ```sql
  /* segment_wise_monthly_sales_and_profit */
  /* Tracks monthly sales and profit performance for each customer segment. */
  CREATE OR REPLACE VIEW segment_wise_monthly_sales_and_profit AS
  SELECT
      c.segment,
      TO_CHAR(o.order_date, 'Mon') AS month_name,
      SUM(o.sales) AS total_sales,
      SUM(o.profit) AS total_profit
  FROM orders AS o
  JOIN customers AS c
  ON o.customer_id = c.customer_id
  GROUP BY c.segment, month_name;
  ```

  ```sql
  /* region_wise_monthly_sales */
  /* Shows monthly sales trends for each region. */
  CREATE OR REPLACE VIEW region_wise_monthly_sales AS
  SELECT
      c.region,
      TO_CHAR(o.order_date, 'Mon') AS month_name,
      SUM(o.sales) AS total_sales
  FROM orders AS o
  JOIN customers AS c
  ON o.customer_id = c.customer_id
  GROUP BY c.region, month_name;
  ```

  ```sql
  /* overall_customers_performance */
  /* Calculates average sales, profit, orders and quantity per customer. */
  CREATE OR REPLACE VIEW overall_customers_performance AS
  SELECT 
      ROUND(SUM(o.sales)/COUNT(DISTINCT o.customer_id)) AS avg_sales_per_customer,
      ROUND(SUM(o.profit)/COUNT(DISTINCT o.customer_id)) AS avg_profit_per_customer,
      ROUND(COUNT(DISTINCT order_id)/COUNT(DISTINCT customer_id)) AS avg_orders_per_customer,
      ROUND(SUM(o.quantity)/COUNT(DISTINCT o.customer_id)) AS avg_quantity_per_customer
  FROM orders AS o;
  ```

  ```sql
  /* avg_discount_per_order_per_customer */
  /* Computes the average discount per customer across all orders. */
  CREATE OR REPLACE VIEW avg_discount_per_order_per_customer AS
  SELECT
      ROUND(AVG(customer_avg), 2) AS avg_discount_per_customer
  FROM (
      SELECT customer_id, AVG(discount) AS customer_avg
      FROM orders
      GROUP BY customer_id
  ) AS sub;
  ```

  ```sql
  /* category_wise_monthly_sales_and_profit */
  /* Tracks monthly sales and profit for each product category. */
  CREATE OR REPLACE VIEW category_wise_monthly_sales_and_profit AS
  SELECT
      p.category,
      TO_CHAR(o.order_date, 'Mon') AS month,
      SUM(o.sales) AS total_sales,
      SUM(o.profit) AS total_profit
  FROM orders AS o
  JOIN products AS p
  ON o.product_id = p.product_id
  GROUP BY p.category, month;
  ```

  ```sql
  /* sub_category_wise_sales_and_profit */
  /* Summarizes total sales and profit by product sub-category. */
  CREATE OR REPLACE VIEW sub_category_wise_sales_and_profit AS
  SELECT
      p.sub_category,
      SUM(o.sales) AS total_sales,
      SUM(o.profit) AS total_profit
  FROM orders AS o
  JOIN products AS p
  ON o.product_id = p.product_id
  GROUP BY p.sub_category;
  ```

  ```sql
  /* category_wise_sales_profit_and_orders */
  /* Shows total sales, profit and order count by product category. */
  CREATE OR REPLACE VIEW category_wise_sales_profit_and_orders AS
  SELECT
      p.category,
      SUM(o.sales) AS total_sales,
      SUM(o.profit) AS total_profit,
      COUNT(DISTINCT o.order_id) AS total_orders
  FROM orders AS o
  JOIN products AS p
  ON o.product_id = p.product_id
  GROUP BY p.category;
  ```

  ```sql
  /* state_wise_most_purchased_sub_category */
  /* Identifies the most purchased sub-category in each U.S. state. */
  CREATE OR REPLACE VIEW state_wise_most_purchased_sub_category AS
  SELECT
      c.state,
      p.sub_category, 
      SUM(o.quantity) AS quantity_sold,
      RANK() OVER (PARTITION BY c.state ORDER BY SUM(o.quantity) DESC) AS sub_category_rank
  FROM orders AS o
  JOIN products AS p
  ON o.product_id = p.product_id
  JOIN customers AS c
  ON o.customer_id = c.customer_id
  GROUP BY c.state, p.sub_category;
  ```
  
</details>

-------------------------------------------------------------------
## ETL Pipeline
- The ETL (Extract, Transform, Load) Pipeline is the core part of this project.
- It automatically cleans and loads validated sales data into a PostgreSQL database for Power BI dashboard.
- It is built with Python using SQLAlchemy and is reliable and securely configured via environment variables.

### ETL Pipeline Structure
| **Script Name**    | **Purpose**                                                                                    |
| :----------------- | :--------------------------------------------------------------------------------------------- |
| `etl.py`           | Sets up the database schema, cleans the dataset and loads initial data into the database.      |
| `create_views.py`  | Creates multiple SQL views that summarizes and aggregates data for Power BI Dashboard.         |
| `generate_data.py` | Generates random synthetic transactions data to simulate daily updates in the database.        |

### How the ETL Pipeline works?
#### 1. `etl.py`
- This script handles the first step of the process, getting your database ready.

<details>
<summary><b>Click Here to view the entire Script</b></summary>
----------------------------------------------------------
  
```python
# ---------------- Loading Python Packages ---------------- 

# Importing Libraries
import os
import sys
import logging
from dotenv import load_dotenv
from sqlalchemy import create_engine, text, inspect

# ---------------- Configuration ---------------- 

# Setting up Logging
LOG_DIR = "logs"
os.makedirs(LOG_DIR, exist_ok=True)
log_file = os.path.join(LOG_DIR, "etl.log")
logging.basicConfig(level=logging.INFO, format="%(asctime)s [%(levelname)s] %(message)s", datefmt="%Y-%m-%d %H:%M:%S",
                    handlers=[
                        logging.FileHandler(log_file, mode="a"),
                        logging.StreamHandler()
                    ])

# Adding utils to PYTHONPATH
sys.path.append(os.path.abspath(os.path.join(os.path.dirname(__file__), "..")))

# Loading Environment File
load_dotenv()

# Loading Database Credentials from Environment File
DB_HOST = os.getenv("DB_HOST")
DB_NAME = os.getenv("DB_NAME")
DB_USER = os.getenv("DB_USER")
DB_PASS = os.getenv("DB_PASS")

# Creating SQLAlchemy Engine to upload the Data to Neon PostgreSQL Database
engine = create_engine(f"postgresql://{DB_USER}:{DB_PASS}@{DB_HOST}/{DB_NAME}?sslmode=require&channel_binding=require", pool_pre_ping=True)
logging.info("SQLAlchemy Engine created successfully")

# ---------------- Check if Table Exists ----------------

# Check if tables already exists in the Neon PostgreSQL Database
inspector = inspect(engine)
existing_tables = inspector.get_table_names()

# If all required tables exist, skip ETL Setup
if set(["customers", "products", "orders"]).issubset(existing_tables):
    logging.info("Tables already exist. Skipping ETL setup.")
else:
    logging.info("Tables do not exist. Running ETL setup...")

    # Reading CSV File
    from utils.read_data import load_csv
    df = load_csv("data", "sales_data.csv")
    logging.info("CSV file loaded successfully")

    # ---------------- Data Cleaning ---------------- 

    # Handling Duplicate Data
    from utils.drop_duplicates import remove_duplicates
    df = remove_duplicates(df)

    # Standardizing Column Names
    from utils.standardize_columns import standardize_column_names
    df = standardize_column_names(df)

    # Optimizing DataFrame
    from utils.data_preprocessor import optimize_dataframe
    df = optimize_dataframe(df)

    logging.info("Data cleaned and optimized successfully")

    # ---------------- Schema Creation ----------------

    # SQL Query to Create Schema
    schema_sql = [
    """
    CREATE TABLE IF NOT EXISTS customers (
        customer_id TEXT PRIMARY KEY,
        customer_name TEXT,
        segment TEXT,
        city TEXT,
        state TEXT,
        country TEXT,
        postal_code NUMERIC,
        region TEXT
    );
    """
    ,
    """
    CREATE TABLE IF NOT EXISTS products (
        product_id TEXT PRIMARY KEY,
        product_name TEXT,
        category TEXT,
        sub_category TEXT
    );
    """
    ,
    """
    CREATE TABLE IF NOT EXISTS orders (
        order_id TEXT PRIMARY KEY,
        order_date DATE,
        customer_id TEXT,
        product_id TEXT,
        ship_mode TEXT,
        ship_date DATE,
        sales NUMERIC,
        quantity INTEGER,
        discount NUMERIC,
        profit NUMERIC,
        FOREIGN KEY (customer_id) REFERENCES customers(customer_id),
        FOREIGN KEY (product_id) REFERENCES products(product_id)
    );
    """]

    # Creating Schema in Neon PostgreSQL Database
    with engine.connect() as conn:
        for stm in schema_sql:
            conn.execute(text(stm))
        conn.commit()
    logging.info("Database schema created successfully")

    # Customers Table
    customers_cols = ["customer_id", "customer_name", "segment", "city", "state", "country", "postal_code", "region"]
    customers = df[customers_cols].drop_duplicates(subset="customer_id")

    # Products Table
    products_cols = ["product_id", "product_name", "category", "sub_category"]
    products = df[products_cols].drop_duplicates(subset="product_id")

    # Orders Table
    orders_cols = ["order_id", "order_date", "customer_id", "product_id", "ship_mode", "ship_date", "sales", "quantity", "discount", "profit"]
    orders = df[orders_cols].drop_duplicates(subset="order_id")

    # Truncate Tables to avoid uploading Duplicate Data
    with engine.begin() as conn:
        conn.execute(text("TRUNCATE TABLE customers CASCADE;"))
        conn.execute(text("TRUNCATE TABLE products CASCADE;"))
        conn.execute(text("TRUNCATE TABLE orders CASCADE;"))
    logging.info("Tables truncated successfully")

    # Appending data to the tables in Neon PostgreSQL Database
    customers.to_sql("customers", engine, if_exists="append", index=False)
    products.to_sql("products", engine, if_exists="append", index=False)
    orders.to_sql("orders", engine, if_exists="append", index=False)
    logging.info("Initial data uploaded to Neon PostgreSQL Database successfully")
```

</details>

#### What is does?
- **Load Configuration**
  - Reads environment variables (like `DB_HOST`, `DB_NAME`) from a `.env` file for secure database access.
- **Logging Setup**
  - Creates a `logs/etl.log` file to track all ETL activity and errors.
- **Extract Data**
  - Loads raw data from a CSV file using a custom `load_csv()` utility function.
- **Transform Data**
  - Removes duplicates, standardizes column names and optimizes datatypes.
- **Load Data**
  - Creates tables in Neon PostgreSQL Database and loads the cleaned data using `to_sql()` function.
- **Schema Management**
  - Ensures relationships between tables using foreign keys and maintains data integrity.
  
-----------------------

#### 2. `create_views.py`
- This script builds SQL views in the PostgreSQL Database to simplify analysis and reporting in Power BI.

<details>
<summary>Click Here to view the entire Script</summary>
<br>
  
```python
# ---------------- Loading Python Packages ---------------- 

# Importing Libraries
import os
import logging
from dotenv import load_dotenv
from sqlalchemy import create_engine, text

# ---------------- Configuration ---------------- 

# Setting up Logging
LOG_DIR = "logs"
os.makedirs(LOG_DIR, exist_ok=True)
log_file = os.path.join(LOG_DIR, "create_views.log")
logging.basicConfig(level=logging.INFO, format="%(asctime)s [%(levelname)s] %(message)s", datefmt="%Y-%m-%d %H:%M:%S",
                    handlers=[
                        logging.FileHandler(log_file, mode="a"),
                        logging.StreamHandler()
                    ])

# Loading Environment File
load_dotenv()

# Loading Database Credentials from Environment File
DB_HOST = os.getenv("DB_HOST")
DB_NAME = os.getenv("DB_NAME")
DB_USER = os.getenv("DB_USER")
DB_PASS = os.getenv("DB_PASS")

# Creating SQLAlchemy Engine to upload the Data to Neon PostgreSQL Database
engine = create_engine(f"postgresql://{DB_USER}:{DB_PASS}@{DB_HOST}/{DB_NAME}?sslmode=require&channel_binding=require", pool_pre_ping=True)
logging.info("SQLAlchemy Engine created successfully")

# ---------------- Creating Views in Neon PostgreSQL Database ----------------

# Dictionary of SQL Views
sql_views = {
    "segment_wise_sales_and_profit":"""
        DROP VIEW IF EXISTS segment_wise_sales_and_profit;
        CREATE OR REPLACE VIEW segment_wise_sales_and_profit AS
        SELECT 
            c.segment,
            SUM(o.sales) AS total_sales,
            SUM(o.profit) AS total_profit
        FROM orders AS o
        JOIN customers AS c
        ON o.customer_id = c.customer_id
        GROUP BY c.segment;
    """
    ,
    "region_wise_sales_and_profit":"""
        DROP VIEW IF EXISTS region_wise_sales_and_profit;
        CREATE OR REPLACE VIEW region_wise_sales_and_profit AS
        SELECT 
            c.region,
            SUM(o.sales) AS total_sales,
            SUM(o.profit) AS total_profit
        FROM orders AS o
        JOIN customers AS c
        ON o.customer_id = c.customer_id
        GROUP BY c.region;
    """
    ,
    "month_wise_sales_and_profit":"""
        DROP VIEW IF EXISTS month_wise_sales_and_profit;
        CREATE OR REPLACE VIEW month_wise_sales_and_profit AS 
        SELECT
            TO_CHAR(order_date, 'Mon') AS month, 
            SUM(sales) AS total_sales,
            SUM(profit) AS total_profit
        FROM orders
        GROUP BY month;
    """
    ,
    "top_customers_by_sales":"""
        DROP VIEW IF EXISTS top_customers_by_sales;
        CREATE OR REPLACE VIEW top_customers_by_sales AS
        SELECT
            c.customer_id, 
            c.customer_name,
            SUM(o.sales) AS total_sales,
            SUM(o.profit) AS total_profit
        FROM orders AS o
        JOIN customers AS c
        ON o.customer_id = c.customer_id
        GROUP BY c.customer_id, c.customer_name;
    """
    ,
    "shipping_performance":"""
        DROP VIEW IF EXISTS shipping_performance;
        CREATE OR REPLACE VIEW shipping_performance AS 
        SELECT 
            ship_mode,
            SUM(sales) AS total_sales,
            SUM(profit) AS total_profit
        FROM orders
        GROUP BY ship_mode;
    """
    ,
    "overall_sales_performance":"""
        DROP VIEW IF EXISTS overall_sales_performance;
        CREATE OR REPLACE VIEW overall_sales_performance AS
        SELECT
            SUM(sales) AS total_sales,
            SUM(profit) AS total_profit,
            COUNT(DISTINCT order_id) AS total_orders,
            COUNT(DISTINCT customer_id) AS total_customers,
            COUNT(DISTINCT product_id) AS total_products,
            SUM(quantity) AS total_quantity_sold
        FROM orders;
    """
    ,
    "state_wise_sales_and_customer_base":"""
        DROP VIEW IF EXISTS state_wise_sales_and_customer_base;
        CREATE OR REPLACE VIEW state_wise_sales_and_customer_base AS 
        SELECT
            c.state,
            SUM(o.sales) AS total_sales,
            COUNT(DISTINCT c.customer_id) AS total_customers
        FROM orders AS o
        JOIN customers AS c
        ON o.customer_id = c.customer_id
        GROUP BY c.state;
    """
    ,
    "segment_wise_monthly_sales_and_profit":"""
        DROP VIEW IF EXISTS segment_wise_monthly_sales_and_profit;
        CREATE OR REPLACE VIEW segment_wise_monthly_sales_and_profit AS
        SELECT
            c.segment,
            TO_CHAR(o.order_date, 'Mon') AS month_name,
            SUM(o.sales) AS total_sales,
            SUM(o.profit) AS total_profit
        FROM orders AS o
        JOIN customers AS c
        ON o.customer_id = c.customer_id
        GROUP BY c.segment, month_name;
    """
    ,
    "region_wise_monthly_sales":"""
        DROP VIEW IF EXISTS region_wise_monthly_sales;
        CREATE OR REPLACE VIEW region_wise_monthly_sales AS
        SELECT
            c.region,
            TO_CHAR(o.order_date, 'Mon') AS month_name,
            SUM(o.sales) AS total_sales
        FROM orders AS o
        JOIN customers AS c
        ON o.customer_id = c.customer_id
        GROUP BY c.region, month_name;
    """
    ,
    "overall_customers_performance":"""
        DROP VIEW IF EXISTS overall_customers_performance;
        CREATE OR REPLACE VIEW overall_customers_performance AS
        SELECT 
            ROUND(SUM(o.sales)/COUNT(DISTINCT o.customer_id)) AS avg_sales_per_customer,
            ROUND(SUM(o.profit)/COUNT(DISTINCT o.customer_id)) AS avg_profit_per_customer,
            ROUND(COUNT(DISTINCT order_id)/COUNT(DISTINCT customer_id)) AS avg_orders_per_customer,
            ROUND(SUM(o.quantity)/COUNT(DISTINCT o.customer_id)) AS avg_quantity_per_customer
        FROM orders AS o;
    """
    ,
    "avg_discount_per_order_per_customer":"""
        DROP VIEW IF EXISTS avg_discount_per_order_per_customer;
        CREATE OR REPLACE VIEW avg_discount_per_order_per_customer AS
        SELECT
            ROUND(AVG(customer_avg), 2) AS avg_discount_per_customer
        FROM (
            SELECT customer_id, AVG(discount) AS customer_avg
            FROM orders
            GROUP BY customer_id
        ) AS sub;
    """
    ,
    "category_wise_monthly_sales_and_profit":"""
        DROP VIEW IF EXISTS category_wise_monthly_sales_and_profit;
        CREATE OR REPLACE VIEW category_wise_monthly_sales_and_profit AS
        SELECT
            p.category,
            TO_CHAR(o.order_date, 'Mon') AS month,
            SUM(o.sales) AS total_sales,
            SUM(o.profit) AS total_profit
        FROM orders AS o
        JOIN products AS p
        ON o.product_id = p.product_id
        GROUP BY p.category, month;
    """
    ,
    "sub_category_wise_sales_and_profit":"""
        DROP VIEW IF EXISTS sub_category_wise_sales_and_profit;
        CREATE OR REPLACE VIEW sub_category_wise_sales_and_profit AS
        SELECT
            p.sub_category,
            SUM(o.sales) AS total_sales,
            SUM(o.profit) AS total_profit
        FROM orders AS o
        JOIN products AS p
        ON o.product_id = p.product_id
        GROUP BY p.sub_category;
    """
    ,
    "category_wise_sales_profit_and_orders":"""
        DROP VIEW IF EXISTS category_wise_sales_profit_and_orders;
        CREATE OR REPLACE VIEW category_wise_sales_profit_and_orders AS
        SELECT
            p.category,
            SUM(o.sales) AS total_sales,
            SUM(o.profit) AS total_profit,
            COUNT(DISTINCT o.order_id) AS total_orders
        FROM orders AS o
        JOIN products AS p
        ON o.product_id = p.product_id
        GROUP BY p.category;
    """
    ,
    "state_wise_most_purchased_sub_category":"""
        DROP VIEW IF EXISTS state_wise_most_purchased_sub_category;
        CREATE OR REPLACE VIEW state_wise_most_purchased_sub_category AS
        SELECT
            c.state,
            p.sub_category, 
            SUM(o.quantity) AS quantity_sold,
            RANK() OVER (PARTITION BY c.state ORDER BY SUM(o.quantity) DESC) AS sub_category_rank
        FROM orders AS o
        JOIN products AS p
        ON o.product_id = p.product_id
        JOIN customers AS c
        ON o.customer_id = c.customer_id
        GROUP BY c.state, p.sub_category;
    """
}

# Creating Views in Neon PostgreSQL Database
with engine.connect() as conn:
    for view_name, sql_query in sql_views.items():
        conn.execute(text(sql_query))
    conn.commit()
logging.info("Views created successfully in Neon PostgreSQL Database")
```
</details>

#### What it does?
- **Database Connection**
  - Connects to the database securely using environment variables.
- **Define SQL Views**
  - Creates multiple SQL views to extract key business insights.
- **Execute & Commit**
  - Executes each `CREATE OR REPLACE VIEW` statement and commit changes.
- **Logging**
  - Stores execution logs in `logs/create_views.log`.

---

#### 3. `generate_data.py`
- This script keeps the database updated with new transaction data for schedule data refresh in Power BI.

<details>
<summary>Click Here to view the entire Script</summary>
<br>
  
```python
# ---------------- Loading Python Packages ---------------- 

# Importing Libraries
import os
import sys
import logging
from dotenv import load_dotenv
from sqlalchemy import create_engine

# ---------------- Configuration ---------------- 

# Setting up Logging
LOG_DIR = "logs"
os.makedirs(LOG_DIR, exist_ok=True)
log_file = os.path.join(LOG_DIR, "generate_data.log")
logging.basicConfig(level=logging.INFO, format="%(asctime)s [%(levelname)s] %(message)s", datefmt="%Y-%m-%d %H:%M:%S",
                    handlers=[
                        logging.FileHandler(log_file, mode="a"),
                        logging.StreamHandler()
                    ])

# Adding utils to PYTHONPATH
sys.path.append(os.path.abspath(os.path.join(os.path.dirname(__file__), "..")))

# Loading Environment File
load_dotenv()

# Loading Database Credentials from Environment File
DB_HOST = os.getenv("DB_HOST")
DB_NAME = os.getenv("DB_NAME")
DB_USER = os.getenv("DB_USER")
DB_PASS = os.getenv("DB_PASS")

# Creating SQLAlchemy Engine to upload the Data to Neon PostgreSQL Database
engine = create_engine(f"postgresql://{DB_USER}:{DB_PASS}@{DB_HOST}/{DB_NAME}?sslmode=require&channel_binding=require", pool_pre_ping=True)
logging.info("SQLAlchemy Engine created successfully")

# ---------------- Generating Random Customers Data ---------------- 

# Generating New Customers Data Randomly
from utils.generate_random_data import random_customers_data
customers = random_customers_data(20, 40)
logging.info("Random customers data generated successfully")

# ---------------- Generating Random Orders Data ---------------- 

# Generating New Orders Data Randomly
from utils.generate_random_data import random_orders_data
orders = random_orders_data(60, 80)
logging.info("Random orders data generated successfully")

# ---------------- Data Cleaning ---------------- 

# Handling Duplicate Data
from utils.drop_duplicates import remove_duplicates
customers = remove_duplicates(customers)
orders = remove_duplicates(orders)

# Standardizing Column Names
from utils.standardize_columns import standardize_column_names
customers = standardize_column_names(customers)
orders = standardize_column_names(orders)

# Optimizing DataFrame
from utils.data_preprocessor import optimize_customers, optimize_orders
customers = optimize_customers(customers)
orders = optimize_orders(orders)

logging.info("Data cleaned and optimized successfully")

# ---------------- Appending New Data to Neon PostgreSQL Database ---------------- 

# Appending new and unique data to Neon PostgreSQL Database
from utils.append_data import append_unique_data_to_db
append_unique_data_to_db("customers", customers, "customer_id", engine)
append_unique_data_to_db("orders", orders, "order_id", engine)
logging.info("New data uploaded to database successfully")
```
</details>

#### What it does?
- **Generate Random Data**
  - Uses custom utility functions to create synthetic customers and orders data.
- **Data Cleaning**
  - Removes duplicates and optimizes datatypes before uploading to Neon Database.
- **Append Unique Data**
  - Inserts only new records into the database, avoiding duplicates.
- **Logging**
  - Saves process logs in `logs/generate_data.log`.

Once this cycle is complete, the process repeats automatically :

```
generate_data.py → create_views.py → Power BI Refresh → New Insights
```

This ensures that the Power BI Dashboard always display the latest insights automatically.

