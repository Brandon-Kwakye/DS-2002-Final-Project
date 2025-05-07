# DS-2002-Final-Project
# Part 1
#### (Download the .ipynb file to see the full thing, in case GitHub won't show the whole file in preview mode!)

#### **1. Overview**
This project demonstrates an ETL (Extract, Transform, Load) pipeline using MySQL, MongoDB, and local file system data sources to populate a dimensional data mart. The goal is to process structured and semi-structured data from multiple sources and store it in a data warehouse for analysis.
This project designs a dimensional data mart for customer order analysis in a retail business. The data mart is structured to support historical sales performance tracking, customer purchasing behavior, and inventory insights.

#### **2. Data Sources**
- **Provided Scripts**
  - AdventureWoks_MySQL(Source Database)
  - AdventureWorks_Queries_MySQL(Views for AdventureWorks)
  - Lab_02c_Create_Populate_Dim_Date(Date)
- **MySQL (AdventureWorks_DW database)**
  - Extracted `fact_sales_orders` (Fact Table)
  - Extracted `dim_customers` (Dimension Table)
  - Extracted `dim_products` (Dimension Table)
  - Extracted `dim_date` (Date Dimension)
- **MongoDB**
  - Inserted `fact_sales_orders`, `dim_customers`, and `dim_products` into MongoDB collections
  - Retrieved and verified the data from MongoDB
- **Local File System**
  - Saved `dim_products.csv` locally for further analysis


### **3. ETL Process**
#### **Step 1: Extract Data**
- Queried MySQL database to retrieve fact and dimension tables.
- Transformed data to align with the data warehouse schema.
- Saved extracted data in JSON format before inserting it into MongoDB.

#### **Step 2: Transform Data**
- Renamed columns to maintain consistency.
- Established primary and foreign key relationships.
- Mapped `order_date_key` using `dim_date` to standardize dates.

#### **Step 3: Load Data**
- Inserted JSON data into MongoDB.
- Exported the `dim_products` dimension as a CSV file.



### **4. Deployment Strategy**
- Connections to MySQL and MongoDB are handled via helper functions (`get_sql_dataframe()`, `get_mongo_dataframe()`, etc.).
- Data is exported in multiple formats for different use cases (JSON for MongoDB, CSV for local storage).
- The process follows **Lab 3 and Lab 4 methodologies**, ensuring compliance with project requirements.


### **5. Verification and Testing**
- **SQL Queries** were used to verify the integrity of foreign key mappings.
- **DataFrames** were displayed after each major step to confirm transformations.
- **MongoDB `count_documents()`** was used to confirm successful insertion.
- **CSV file validation** ensured the local file was saved and accessible.

# Part 2
# Data Lakehouse Implementation with Spark Streaming

This solution demonstrates a complete implementation of the **Databricks Lakehouse architecture** (Bronze → Silver → Gold) using Apache Spark. It processes **streaming fact data** alongside **batch reference data**, integrating them into a unified analytical pipeline that supports incremental data loading and real-time aggregation.


## Functional Requirements Coverage

1. **Batch Execution with Incremental Load**  
   The reference dimension tables (`dim_customers`, `dim_products`, and `dim_date`) were loaded via batch from CSV and SQL sources, representing **static data** used to enrich the fact table during Silver layer transformations.

2. **Streaming Real-Time Fact Data**  
   The `fact_sales_orders` table was split into **three separate JSON files**, simulating incremental real-time data from a streaming source. Spark AutoLoader and `.readStream()` were used to ingest this data as a **structured streaming source**, enabling mini-batch processing.

3. **Bronze Layer**  
   The streaming JSON files were written to the **Bronze layer** in Parquet format, appending metadata such as `receipt_time` and `source_file` for traceability.

4. **Silver Layer**  
   The Bronze data was enriched by **joining with reference dimension tables** using appropriate foreign keys and date transformations. The resulting Silver table reflects **business-ready transactional data**, including fields like `order_full_date`, `product_key`, and `customer_key`.

5. **Gold Layer**  
   Aggregated tables were built from the Silver layer:
   Focused on **products**, grouping by `ProductCategory` and `month_name`.

6. **Execution in a Databricks Notebook**  
   All operations — including data ingestion, transformation, streaming queries, and output generation — were performed in a single Databricks notebook using PySpark.
