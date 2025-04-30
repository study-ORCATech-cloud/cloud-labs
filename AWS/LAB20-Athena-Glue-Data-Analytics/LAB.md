# LAB20: Serverless Data Analytics with Amazon Athena and AWS Glue

## ⚡ Lab Overview

In this advanced lab, you'll work with **Amazon Athena** and **AWS Glue** to build a complete serverless data analytics solution. You'll learn how to discover, catalog, transform, and query data stored in Amazon S3 without managing any infrastructure, enabling you to extract valuable insights from your data lake efficiently and cost-effectively.

### 💼 Real-World DevOps Applications

Athena and Glue form the backbone of modern serverless data analytics platforms, offering several key advantages:

- **Zero Infrastructure Management**: Focus on data and insights, not servers
- **Cost Efficiency**: Pay only for what you use with no idle resources
- **Scalability**: Automatically scales to match your data volume
- **Integration**: Works seamlessly with existing AWS services
- **Flexibility**: Support for diverse data formats and sources
- **Automation**: Scheduled workflows with minimal human intervention

Common production use cases include:

- **Log Analysis**: Query and analyze application and infrastructure logs
- **Business Intelligence**: Extract insights from business data
- **Data Lakes**: Implement flexible, scalable data storage and analytics
- **ETL Workflows**: Transform data for analytics or reporting
- **Ad-hoc Analysis**: Quickly explore and query data without provisioning
- **Cost Analytics**: Analyze AWS usage and costs across organization
- **IoT Data Processing**: Process and analyze data from IoT devices

---

## 🎯 Objectives

By the end of this lab, you will be able to:
- Create and configure an S3-based data lake with raw and processed zones
- Set up Glue databases, crawlers, and tables to discover and catalog data
- Create and run Glue ETL jobs to transform data into analytics-optimized formats
- Write and execute SQL queries in Athena to analyze the data
- Implement partitioning strategies for performance and cost optimization
- Schedule automated data processing workflows
- Apply best practices for data lake organization and security
- Analyze various data formats including CSV, JSON, and Parquet

---

## 🛠️ Prerequisites

- AWS account with administrative permissions
- Basic understanding of SQL and data analytics concepts
- Familiarity with AWS services (particularly S3)
- AWS CLI installed and configured (refer to [CLI Setup Guide](../../CLI-Setup.md))
- Basic understanding of data formats (CSV, JSON, Parquet)

---

# 🌐 PART 1: AWS MANAGEMENT CONSOLE IMPLEMENTATION

In this first part of the lab, you'll create and configure Athena and Glue resources using the AWS Management Console.

## 📊 Step 1: Set Up S3 Buckets for Data Lake Structure

First, let's create the S3 buckets that will form our data lake:

1. Sign in to the AWS Management Console
2. Navigate to the **Amazon S3** service
3. Click **Create bucket**
4. For **Bucket name**, enter `lab20-data-lake-[RANDOM_STRING]` (replace [RANDOM_STRING] with a unique identifier, like your initials and a random number)
5. For **AWS Region**, select **EU (Ireland) eu-west-1**
6. Keep default settings for all other options
7. Click **Create bucket**

Now, create the following folder structure in your bucket:
1. Select the bucket you just created
2. Click **Create folder**
3. Create the following folders:
   - `raw/sales/` (for raw sales data)
   - `raw/customers/` (for raw customer data)
   - `processed/sales/` (for processed sales data)
   - `processed/customers/` (for processed customer data)
   - `athena-results/` (for Athena query results)

## 📁 Step 2: Upload Sample Data to the S3 Bucket

Now, let's upload some sample data files to work with:

1. Create a file called `customers.csv` on your local machine with the following content:

```csv
customer_id,first_name,last_name,email,registration_date,country
1,John,Smith,john.smith@example.com,2022-01-15,USA
2,Maria,Garcia,maria.garcia@example.com,2022-01-18,Spain
3,Ahmed,Khan,ahmed.khan@example.com,2022-01-20,Pakistan
4,Yuki,Tanaka,yuki.tanaka@example.com,2022-01-22,Japan
5,Emma,Johnson,emma.johnson@example.com,2022-01-25,UK
6,Carlos,Rodriguez,carlos.rodriguez@example.com,2022-01-28,Mexico
7,Sophie,Dupont,sophie.dupont@example.com,2022-01-30,France
8,Li,Wei,li.wei@example.com,2022-02-02,China
9,Michael,Brown,michael.brown@example.com,2022-02-05,Canada
10,Anna,Kowalski,anna.kowalski@example.com,2022-02-08,Poland
```

2. Create a file called `sales_2023_01.json` with the following content:

```json
{"sale_id": 1001, "customer_id": 1, "product": "Laptop", "quantity": 1, "price": 1200.00, "sale_date": "2023-01-05", "region": "North America"}
{"sale_id": 1002, "customer_id": 3, "product": "Smartphone", "quantity": 1, "price": 800.00, "sale_date": "2023-01-07", "region": "Asia"}
{"sale_id": 1003, "customer_id": 5, "product": "Headphones", "quantity": 2, "price": 150.00, "sale_date": "2023-01-10", "region": "Europe"}
{"sale_id": 1004, "customer_id": 2, "product": "Monitor", "quantity": 1, "price": 350.00, "sale_date": "2023-01-12", "region": "Europe"}
{"sale_id": 1005, "customer_id": 8, "product": "Tablet", "quantity": 1, "price": 500.00, "sale_date": "2023-01-15", "region": "Asia"}
```

3. Create a file called `sales_2023_02.json` with the following content:

```json
{"sale_id": 1006, "customer_id": 4, "product": "Laptop", "quantity": 1, "price": 1300.00, "sale_date": "2023-02-03", "region": "Asia"}
{"sale_id": 1007, "customer_id": 7, "product": "Smartphone", "quantity": 1, "price": 750.00, "sale_date": "2023-02-08", "region": "Europe"}
{"sale_id": 1008, "customer_id": 9, "product": "Tablet", "quantity": 1, "price": 450.00, "sale_date": "2023-02-12", "region": "North America"}
{"sale_id": 1009, "customer_id": 10, "product": "Headphones", "quantity": 2, "price": 160.00, "sale_date": "2023-02-18", "region": "Europe"}
{"sale_id": 1010, "customer_id": 6, "product": "Monitor", "quantity": 2, "price": 320.00, "sale_date": "2023-02-22", "region": "North America"}
```

4. Upload these files to your S3 bucket:
   - Upload `customers.csv` to the `raw/customers/` folder
   - Upload `sales_2023_01.json` to the `raw/sales/year=2023/month=01/` folder (create these subfolders)
   - Upload `sales_2023_02.json` to the `raw/sales/year=2023/month=02/` folder (create these subfolders)

## 🏢 Step 3: Create a Database in AWS Glue Data Catalog

Now, let's create a database in the AWS Glue Data Catalog:

1. Navigate to **AWS Glue** service
2. In the left navigation pane, under **Data catalog**, click **Databases**
3. Click **Add database**
4. For **Database name**, enter `lab20_analytics_db`
5. For **Description**, enter `Database for LAB20 Analytics`
6. Click **Create database**

## 🕸️ Step 4: Create and Run a Glue Crawler for Customer Data

Let's create a crawler to discover and catalog the customer data:

1. In the AWS Glue console, go to **Crawlers** in the left navigation pane
2. Click **Create crawler**
3. For **Crawler name**, enter `lab20_customers_crawler`
4. Click **Next**
5. For **Crawler source type**, select **Data stores**
6. Click **Next**
7. For **Choose a data store**, select **S3**
8. For **Connection**, leave as **Not required**
9. For **Include path**, enter `s3://lab20-data-lake-[RANDOM_STRING]/raw/customers/` (replace with your actual bucket name)
10. Click **Next**
11. For **Add another data store**, select **No**
12. Click **Next**
13. For **IAM role**, select **Create an IAM role**
14. For **Name suffix**, enter `lab20-crawler-role`
15. Click **Next**
16. For **Frequency**, select **Run on demand**
17. Click **Next**
18. For **Database**, select the `lab20_analytics_db` database you created earlier
19. Expand **Advanced options** and check **Create a single schema for each S3 path**
20. Click **Next**
21. Review the settings and click **Create crawler**

Now, run the crawler:
1. Select the `lab20_customers_crawler` you just created
2. Click **Run crawler**
3. Wait for the crawler to complete (this may take a minute or two)

## 🕸️ Step 5: Create and Run a Glue Crawler for Sales Data

Now let's create a crawler for the sales data with partitioning:

1. In the AWS Glue console, go to **Crawlers**
2. Click **Create crawler**
3. For **Crawler name**, enter `lab20_sales_crawler`
4. Click **Next**
5. For **Crawler source type**, select **Data stores**
6. Click **Next**
7. For **Choose a data store**, select **S3**
8. For **Connection**, leave as **Not required**
9. For **Include path**, enter `s3://lab20-data-lake-[RANDOM_STRING]/raw/sales/` (replace with your actual bucket name)
10. Click **Next**
11. For **Add another data store**, select **No**
12. Click **Next**
13. For **IAM role**, select the role created earlier (`AWSGlueServiceRole-lab20-crawler-role`)
14. Click **Next**
15. For **Frequency**, select **Run on demand**
16. Click **Next**
17. For **Database**, select the `lab20_analytics_db` database
18. Expand **Advanced options** and configure:
    - Check **Create a single schema for each S3 path**
    - Check **Enable partition detection**
    - For **Partition structure**, enter `year/month`
19. Click **Next**
20. Review the settings and click **Create crawler**

Now, run the crawler:
1. Select the `lab20_sales_crawler` you just created
2. Click **Run crawler**
3. Wait for the crawler to complete

## 🔍 Step 6: Examine the Tables Created in the Glue Data Catalog

Let's look at the tables that were created by the crawlers:

1. In the AWS Glue console, go to **Tables** in the left navigation pane
2. You should see two tables:
   - `customers`: Contains customer data
   - `sales`: Contains sales data with year and month partitions

3. Click on the `customers` table to view its details:
   - Check the schema to verify the columns were detected correctly
   - Note the data format and location information

4. Click on the `sales` table to view its details:
   - Check the schema
   - Examine the partition keys (year and month)
   - Note that the data is in JSON format

## 🔄 Step 7: Create a Glue ETL Job to Convert Data to Parquet Format

Now, let's create an ETL job to convert our JSON sales data to the more efficient Parquet format:

1. In the AWS Glue console, go to **ETL Jobs** in the left navigation pane
2. Click **Create job**
3. For **Job name**, enter `lab20_sales_to_parquet`
4. For **IAM Role**, select the role created earlier (`AWSGlueServiceRole-lab20-crawler-role`)
5. For **Type**, select **Spark**
6. For **Glue version**, select the latest version
7. For **Language**, select **Python**
8. For **Generated script**, select **Script editor**
9. Click **Create**
10. In the script editor, replace the default script with the following Python code:

```python
import sys
from awsglue.transforms import *
from awsglue.utils import getResolvedOptions
from pyspark.context import SparkContext
from awsglue.context import GlueContext
from awsglue.job import Job
from awsglue.dynamicframe import DynamicFrame

# Initialize GlueContext
sc = SparkContext()
glueContext = GlueContext(sc)
spark = glueContext.spark_session
job = Job(glueContext)

# Replace with your actual bucket name
bucket_name = "lab20-data-lake-[RANDOM_STRING]"

# Source and target paths
source_path = f"s3://{bucket_name}/raw/sales/"
target_path = f"s3://{bucket_name}/processed/sales/"

# Read from catalog
sales_data = glueContext.create_dynamic_frame.from_catalog(
    database="lab20_analytics_db",
    table_name="sales"
)

# Keep track of the partition information
partition_keys = ["year", "month"]

# Convert to Parquet format and write to target with partitioning
glueContext.write_dynamic_frame.from_options(
    frame=sales_data,
    connection_type="s3",
    connection_options={
        "path": target_path,
        "partitionKeys": partition_keys
    },
    format="parquet"
)

print("Conversion to Parquet completed successfully!")
job.commit()
```

11. Click **Save**
12. Replace `lab20-data-lake-[RANDOM_STRING]` in the script with your actual bucket name
13. Click **Run**
14. Wait for the job to complete

Similarly, let's create an ETL job for customers data:

1. Click **Create job**
2. For **Job name**, enter `lab20_customers_to_parquet`
3. For **IAM Role**, select the role created earlier
4. For **Type**, select **Spark**
5. For **Glue version**, select the latest version
6. For **Language**, select **Python**
7. For **Generated script**, select **Script editor**
8. Click **Create**
9. In the script editor, replace the default script with the following Python code:

```python
import sys
from awsglue.transforms import *
from awsglue.utils import getResolvedOptions
from pyspark.context import SparkContext
from awsglue.context import GlueContext
from awsglue.job import Job

# Initialize GlueContext
sc = SparkContext()
glueContext = GlueContext(sc)
spark = glueContext.spark_session
job = Job(glueContext)

# Replace with your actual bucket name
bucket_name = "lab20-data-lake-[RANDOM_STRING]"

# Source and target paths
source_path = f"s3://{bucket_name}/raw/customers/"
target_path = f"s3://{bucket_name}/processed/customers/"

# Read from catalog
customers_data = glueContext.create_dynamic_frame.from_catalog(
    database="lab20_analytics_db",
    table_name="customers"
)

# Convert to Parquet format and write to target
glueContext.write_dynamic_frame.from_options(
    frame=customers_data,
    connection_type="s3",
    connection_options={
        "path": target_path
    },
    format="parquet"
)

print("Conversion to Parquet completed successfully!")
job.commit()
```

10. Click **Save**
11. Replace `lab20-data-lake-[RANDOM_STRING]` in the script with your actual bucket name
12. Click **Run**
13. Wait for the job to complete

## 🕸️ Step 8: Create Crawlers for the Processed Data

Now that we have processed data in Parquet format, let's create crawlers to catalog it:

1. In the AWS Glue console, go to **Crawlers**
2. Click **Create crawler**
3. For **Crawler name**, enter `lab20_processed_sales_crawler`
4. Click **Next**
5. For **Crawler source type**, select **Data stores**
6. Click **Next**
7. For **Choose a data store**, select **S3**
8. For **Include path**, enter `s3://lab20-data-lake-[RANDOM_STRING]/processed/sales/` (replace with your actual bucket name)
9. Click **Next**
10. For **Add another data store**, select **No**
11. Click **Next**
12. For **IAM role**, select the role created earlier
13. Click **Next**
14. For **Frequency**, select **Run on demand**
15. Click **Next**
16. For **Database**, select the `lab20_analytics_db` database
17. For **Table name prefix**, enter `processed_`
18. Expand **Advanced options** and check **Enable partition detection**
19. For **Partition structure**, enter `year/month`
20. Click **Next**
21. Review the settings and click **Create crawler**

Similarly, create a crawler for processed customer data:

1. Click **Create crawler**
2. For **Crawler name**, enter `lab20_processed_customers_crawler`
3. Click **Next**
4. For **Crawler source type**, select **Data stores**
5. Click **Next**
6. For **Choose a data store**, select **S3**
7. For **Include path**, enter `s3://lab20-data-lake-[RANDOM_STRING]/processed/customers/` (replace with your actual bucket name)
8. Click **Next**
9. For **Add another data store**, select **No**
10. Click **Next**
11. For **IAM role**, select the role created earlier
12. Click **Next**
13. For **Frequency**, select **Run on demand**
14. Click **Next**
15. For **Database**, select the `lab20_analytics_db` database
16. For **Table name prefix**, enter `processed_`
17. Click **Next**
18. Review the settings and click **Create crawler**

Now, run both crawlers:
1. Select both crawlers
2. Click **Run crawler**
3. Wait for the crawlers to complete

## 🪄 Step 9: Configure Athena for Query Results

Before we run queries, let's configure Athena to store query results:

1. Navigate to the **Amazon Athena** service
2. If this is your first time using Athena in this region, click **Get Started** or **Edit settings**
3. In the **Settings** section, configure the query result location:
   - Enter `s3://lab20-data-lake-[RANDOM_STRING]/athena-results/` (replace with your actual bucket name)
4. Click **Save**

## 📊 Step 10: Run Queries in Athena

Now, let's run some SQL queries in Athena to analyze our data:

1. In the Athena console, ensure that the **lab20_analytics_db** is selected from the database dropdown
2. In the query editor, enter the following SQL to query customer data:

```sql
SELECT * FROM processed_customers LIMIT 10;
```

3. Click **Run query**
4. View the results in the Results pane

5. Now, let's query sales data with partitioning:

```sql
SELECT * FROM processed_sales WHERE year = '2023' AND month = '01' LIMIT 10;
```

6. Click **Run query**
7. View the results in the Results pane

8. Let's run a more complex query that joins data across tables:

```sql
SELECT 
    s.sale_id,
    s.product,
    s.quantity,
    s.price,
    s.sale_date,
    s.region,
    c.first_name,
    c.last_name,
    c.email,
    c.country
FROM 
    processed_sales s
JOIN 
    processed_customers c ON s.customer_id = c.customer_id
WHERE 
    s.year = '2023' AND s.price > 500
ORDER BY 
    s.price DESC;
```

9. Click **Run query**
10. Examine the joined results

11. Let's run an analytical query to get sales statistics by region:

```sql
SELECT 
    region,
    COUNT(*) as num_sales,
    SUM(quantity) as total_items_sold,
    SUM(price * quantity) as total_revenue,
    AVG(price) as avg_price
FROM 
    processed_sales
GROUP BY 
    region
ORDER BY 
    total_revenue DESC;
```

12. Click **Run query**
13. Analyze the aggregated results

## 🔄 Step 11: Create a Scheduled Workflow

Now, let's set up a scheduled workflow to automate the ETL process:

1. In the AWS Glue console, go to **Triggers** in the left navigation pane
2. Click **Add trigger**
3. For **Trigger name**, enter `lab20_daily_processing`
4. For **Trigger type**, select **Schedule**
5. For **Frequency**, select **Daily** and set a time
6. Click **Next**
7. For **Jobs to add**, select both of our ETL jobs:
   - `lab20_sales_to_parquet`
   - `lab20_customers_to_parquet`
8. Click **Next**
9. Review the settings and click **Finish**

## 📊 Step 12: Create a View in Athena

Let's create a view in Athena for our common joined query:

1. In the Athena console, run the following SQL:

```sql
CREATE OR REPLACE VIEW sales_with_customer_details AS
SELECT 
    s.sale_id,
    s.product,
    s.quantity,
    s.price,
    s.sale_date,
    s.region,
    c.first_name,
    c.last_name,
    c.email,
    c.country
FROM 
    processed_sales s
JOIN 
    processed_customers c ON s.customer_id = c.customer_id;
```

2. Click **Run query**
3. After the view is created, you can query it directly:

```sql
SELECT * FROM sales_with_customer_details WHERE price > 1000;
```

4. Click **Run query**
5. View the results 

## 🗑️ Step 13: Clean Up Resources (Console)

To avoid ongoing charges, clean up the resources created in this lab:

1. Delete the Athena workgroup and views:
   - In the Athena console, go to **Workgroups**
   - Select the workgroup you used (primary)
   - Run the following query to drop the view:
   ```sql
   DROP VIEW IF EXISTS sales_with_customer_details;
   ```

2. Delete the Glue triggers:
   - In the AWS Glue console, go to **Triggers**
   - Select the trigger `lab20_daily_processing`
   - Click **Actions** > **Delete trigger**
   - Confirm deletion

3. Delete the Glue jobs:
   - In the AWS Glue console, go to **Jobs**
   - Select the jobs you created (`lab20_sales_to_parquet` and `lab20_customers_to_parquet`)
   - Click **Actions** > **Delete job**
   - Confirm deletion

4. Delete the Glue crawlers:
   - In the AWS Glue console, go to **Crawlers**
   - Select all crawlers created for this lab
   - Click **Actions** > **Delete crawler**
   - Confirm deletion

5. Delete the Glue tables and database:
   - In the AWS Glue console, go to **Tables**
   - Select all tables in the `lab20_analytics_db` database
   - Click **Actions** > **Delete table**
   - Confirm deletion
   - Go to **Databases**
   - Select the `lab20_analytics_db` database
   - Click **Actions** > **Delete database**
   - Confirm deletion

6. Delete the S3 bucket:
   - In the S3 console, select your bucket
   - Click **Empty**
   - Type the confirmation phrase and click **Empty**
   - After emptying, click **Delete**
   - Type the bucket name and click **Delete bucket**

7. Delete the IAM role:
   - In the IAM console, go to **Roles**
   - Search for the role created for this lab (`AWSGlueServiceRole-lab20-crawler-role`)
   - Select the role and click **Delete**
   - Type the confirmation and click **Delete**

---

# 💻 PART 2: AWS CLI IMPLEMENTATION

In this section, you'll perform the same operations using the AWS CLI.

## 📊 Step 1: Set Up S3 Buckets for Data Lake Structure

```bash
# Create S3 bucket (replace [RANDOM_STRING] with a unique identifier)
aws s3 mb s3://lab20-data-lake-cli-[RANDOM_STRING] --region eu-west-1

# Create folder structure
touch empty.tmp
aws s3 cp empty.tmp s3://lab20-data-lake-cli-[RANDOM_STRING]/raw/sales/
aws s3 cp empty.tmp s3://lab20-data-lake-cli-[RANDOM_STRING]/raw/customers/
aws s3 cp empty.tmp s3://lab20-data-lake-cli-[RANDOM_STRING]/processed/sales/
aws s3 cp empty.tmp s3://lab20-data-lake-cli-[RANDOM_STRING]/processed/customers/
aws s3 cp empty.tmp s3://lab20-data-lake-cli-[RANDOM_STRING]/athena-results/
rm empty.tmp

# Delete the placeholder files
aws s3 rm s3://lab20-data-lake-cli-[RANDOM_STRING]/raw/sales/empty.tmp
aws s3 rm s3://lab20-data-lake-cli-[RANDOM_STRING]/raw/customers/empty.tmp
aws s3 rm s3://lab20-data-lake-cli-[RANDOM_STRING]/processed/sales/empty.tmp
aws s3 rm s3://lab20-data-lake-cli-[RANDOM_STRING]/processed/customers/empty.tmp
aws s3 rm s3://lab20-data-lake-cli-[RANDOM_STRING]/athena-results/empty.tmp
```

## 📁 Step 2: Upload Sample Data to S3

Create the sample files (customers.csv, sales_2023_01.json, sales_2023_02.json) as described in the console part, then:

```bash
# Upload customers data
aws s3 cp customers.csv s3://lab20-data-lake-cli-[RANDOM_STRING]/raw/customers/

# Create partition directories and upload sales data
aws s3 cp sales_2023_01.json s3://lab20-data-lake-cli-[RANDOM_STRING]/raw/sales/year=2023/month=01/
aws s3 cp sales_2023_02.json s3://lab20-data-lake-cli-[RANDOM_STRING]/raw/sales/year=2023/month=02/
```

## 🏢 Step 3: Create a Database in AWS Glue Data Catalog

```bash
# Create a database in Glue Data Catalog
aws glue create-database \
    --database-input '{"Name":"lab20_analytics_db_cli","Description":"Database for LAB20 Analytics - CLI version"}' \
    --region eu-west-1
```

## 👮 Step 4: Create IAM Role for Glue

```bash
# Create an IAM policy document for trust relationship
cat > glue-trust-policy.json << 'EOF'
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "glue.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
EOF

# Create the IAM role
aws iam create-role \
    --role-name AWSGlueServiceRole-lab20-cli \
    --assume-role-policy-document file://glue-trust-policy.json

# Attach necessary policies
aws iam attach-role-policy \
    --role-name AWSGlueServiceRole-lab20-cli \
    --policy-arn arn:aws:iam::aws:policy/service-role/AWSGlueServiceRole

aws iam attach-role-policy \
    --role-name AWSGlueServiceRole-lab20-cli \
    --policy-arn arn:aws:iam::aws:policy/AmazonS3FullAccess
```

## 🕸️ Step 5: Create and Run Glue Crawlers

```bash
# Create crawler for customers data
aws glue create-crawler \
    --name lab20_customers_crawler_cli \
    --role "AWSGlueServiceRole-lab20-cli" \
    --database-name "lab20_analytics_db_cli" \
    --targets '{"S3Targets": [{"Path": "s3://lab20-data-lake-cli-[RANDOM_STRING]/raw/customers/"}]}' \
    --schema-change-policy '{"UpdateBehavior": "UPDATE_IN_DATABASE", "DeleteBehavior": "DELETE_FROM_DATABASE"}' \
    --configuration '{"Version": 1.0, "CrawlerOutput": {"Partitions": {"AddOrUpdateBehavior": "InheritFromTable"}, "Tables": {"AddOrUpdateBehavior": "MergeNewColumns"}}}' \
    --region eu-west-1

# Create crawler for sales data with partitioning
aws glue create-crawler \
    --name lab20_sales_crawler_cli \
    --role "AWSGlueServiceRole-lab20-cli" \
    --database-name "lab20_analytics_db_cli" \
    --targets '{"S3Targets": [{"Path": "s3://lab20-data-lake-cli-[RANDOM_STRING]/raw/sales/"}]}' \
    --schema-change-policy '{"UpdateBehavior": "UPDATE_IN_DATABASE", "DeleteBehavior": "DELETE_FROM_DATABASE"}' \
    --configuration '{"Version": 1.0, "CrawlerOutput": {"Partitions": {"AddOrUpdateBehavior": "InheritFromTable"}, "Tables": {"AddOrUpdateBehavior": "MergeNewColumns"}}, "Grouping": {"TableGroupingPolicy": "CombineCompatibleSchemas"}}' \
    --recrawl-policy '{"RecrawlBehavior": "CRAWL_EVERYTHING"}' \
    --region eu-west-1

# Run the crawlers
aws glue start-crawler --name lab20_customers_crawler_cli --region eu-west-1
aws glue start-crawler --name lab20_sales_crawler_cli --region eu-west-1

# Wait for crawlers to complete
echo "Waiting for crawlers to complete..."
aws glue get-crawler --name lab20_customers_crawler_cli --region eu-west-1
aws glue get-crawler --name lab20_sales_crawler_cli --region eu-west-1
```

## 📜 Step 6: Create Glue ETL Jobs

First, create the script files locally:

```bash
# Create Python script for sales ETL job
cat > sales_to_parquet.py << 'EOF'
import sys
from awsglue.transforms import *
from awsglue.utils import getResolvedOptions
from pyspark.context import SparkContext
from awsglue.context import GlueContext
from awsglue.job import Job
from awsglue.dynamicframe import DynamicFrame

# Initialize GlueContext
sc = SparkContext()
glueContext = GlueContext(sc)
spark = glueContext.spark_session
job = Job(glueContext)

# Get job parameters
args = getResolvedOptions(sys.argv, ['JOB_NAME', 'source_path', 'target_path', 'database_name', 'table_name'])

# Source and target paths
source_path = args['source_path']
target_path = args['target_path']
database_name = args['database_name']
table_name = args['table_name']

# Read from catalog
sales_data = glueContext.create_dynamic_frame.from_catalog(
    database=database_name,
    table_name=table_name
)

# Keep track of the partition information
partition_keys = ["year", "month"]

# Convert to Parquet format and write to target with partitioning
glueContext.write_dynamic_frame.from_options(
    frame=sales_data,
    connection_type="s3",
    connection_options={
        "path": target_path,
        "partitionKeys": partition_keys
    },
    format="parquet"
)

print("Conversion to Parquet completed successfully!")
job.commit()
EOF

# Create Python script for customers ETL job
cat > customers_to_parquet.py << 'EOF'
import sys
from awsglue.transforms import *
from awsglue.utils import getResolvedOptions
from pyspark.context import SparkContext
from awsglue.context import GlueContext
from awsglue.job import Job

# Initialize GlueContext
sc = SparkContext()
glueContext = GlueContext(sc)
spark = glueContext.spark_session
job = Job(glueContext)

# Get job parameters
args = getResolvedOptions(sys.argv, ['JOB_NAME', 'source_path', 'target_path', 'database_name', 'table_name'])

# Source and target paths
source_path = args['source_path']
target_path = args['target_path']
database_name = args['database_name']
table_name = args['table_name']

# Read from catalog
customers_data = glueContext.create_dynamic_frame.from_catalog(
    database=database_name,
    table_name=table_name
)

# Convert to Parquet format and write to target
glueContext.write_dynamic_frame.from_options(
    frame=customers_data,
    connection_type="s3",
    connection_options={
        "path": target_path
    },
    format="parquet"
)

print("Conversion to Parquet completed successfully!")
job.commit()
EOF
```

Now, upload the scripts to S3 and create the Glue jobs:

```bash
# Upload scripts to S3
aws s3 cp sales_to_parquet.py s3://lab20-data-lake-cli-[RANDOM_STRING]/scripts/
aws s3 cp customers_to_parquet.py s3://lab20-data-lake-cli-[RANDOM_STRING]/scripts/

# Create the sales ETL job
aws glue create-job \
    --name lab20_sales_to_parquet_cli \
    --role AWSGlueServiceRole-lab20-cli \
    --command '{"Name": "glueetl", "ScriptLocation": "s3://lab20-data-lake-cli-[RANDOM_STRING]/scripts/sales_to_parquet.py", "PythonVersion": "3"}' \
    --default-arguments '{
        "--source_path": "s3://lab20-data-lake-cli-[RANDOM_STRING]/raw/sales/",
        "--target_path": "s3://lab20-data-lake-cli-[RANDOM_STRING]/processed/sales/",
        "--database_name": "lab20_analytics_db_cli",
        "--table_name": "sales",
        "--job-language": "python",
        "--TempDir": "s3://lab20-data-lake-cli-[RANDOM_STRING]/temporary/"
    }' \
    --max-capacity 2.0 \
    --region eu-west-1

# Create the customers ETL job
aws glue create-job \
    --name lab20_customers_to_parquet_cli \
    --role AWSGlueServiceRole-lab20-cli \
    --command '{"Name": "glueetl", "ScriptLocation": "s3://lab20-data-lake-cli-[RANDOM_STRING]/scripts/customers_to_parquet.py", "PythonVersion": "3"}' \
    --default-arguments '{
        "--source_path": "s3://lab20-data-lake-cli-[RANDOM_STRING]/raw/customers/",
        "--target_path": "s3://lab20-data-lake-cli-[RANDOM_STRING]/processed/customers/",
        "--database_name": "lab20_analytics_db_cli",
        "--table_name": "customers",
        "--job-language": "python",
        "--TempDir": "s3://lab20-data-lake-cli-[RANDOM_STRING]/temporary/"
    }' \
    --max-capacity 2.0 \
    --region eu-west-1

# Run the jobs
aws glue start-job-run --job-name lab20_sales_to_parquet_cli --region eu-west-1
aws glue start-job-run --job-name lab20_customers_to_parquet_cli --region eu-west-1

# Check job status
aws glue get-job-runs --job-name lab20_sales_to_parquet_cli --region eu-west-1
aws glue get-job-runs --job-name lab20_customers_to_parquet_cli --region eu-west-1
```

## 🕸️ Step 7: Create Crawlers for Processed Data

```bash
# Create crawler for processed sales data
aws glue create-crawler \
    --name lab20_processed_sales_crawler_cli \
    --role "AWSGlueServiceRole-lab20-cli" \
    --database-name "lab20_analytics_db_cli" \
    --table-prefix "processed_" \
    --targets '{"S3Targets": [{"Path": "s3://lab20-data-lake-cli-[RANDOM_STRING]/processed/sales/"}]}' \
    --schema-change-policy '{"UpdateBehavior": "UPDATE_IN_DATABASE", "DeleteBehavior": "DELETE_FROM_DATABASE"}' \
    --configuration '{"Version": 1.0, "CrawlerOutput": {"Partitions": {"AddOrUpdateBehavior": "InheritFromTable"}, "Tables": {"AddOrUpdateBehavior": "MergeNewColumns"}}}' \
    --region eu-west-1

# Create crawler for processed customers data
aws glue create-crawler \
    --name lab20_processed_customers_crawler_cli \
    --role "AWSGlueServiceRole-lab20-cli" \
    --database-name "lab20_analytics_db_cli" \
    --table-prefix "processed_" \
    --targets '{"S3Targets": [{"Path": "s3://lab20-data-lake-cli-[RANDOM_STRING]/processed/customers/"}]}' \
    --schema-change-policy '{"UpdateBehavior": "UPDATE_IN_DATABASE", "DeleteBehavior": "DELETE_FROM_DATABASE"}' \
    --configuration '{"Version": 1.0, "CrawlerOutput": {"Partitions": {"AddOrUpdateBehavior": "InheritFromTable"}, "Tables": {"AddOrUpdateBehavior": "MergeNewColumns"}}}' \
    --region eu-west-1

# Run the crawlers
aws glue start-crawler --name lab20_processed_sales_crawler_cli --region eu-west-1
aws glue start-crawler --name lab20_processed_customers_crawler_cli --region eu-west-1
```

## 🪄 Step 8: Configure Athena and Run Queries

```bash
# Set up Athena query results location
aws athena update-work-group \
    --work-group primary \
    --configuration '{"ResultConfiguration":{"OutputLocation":"s3://lab20-data-lake-cli-[RANDOM_STRING]/athena-results/"}}' \
    --region eu-west-1

# Execute sample query - list customers (this is an example, actual execution differs)
aws athena start-query-execution \
    --query-string "SELECT * FROM lab20_analytics_db_cli.processed_customers LIMIT 10;" \
    --result-configuration "OutputLocation=s3://lab20-data-lake-cli-[RANDOM_STRING]/athena-results/" \
    --region eu-west-1

# Get query results
aws athena get-query-execution \
    --query-execution-id <QUERY_EXECUTION_ID> \
    --region eu-west-1

# Create a view for joined data (this is an example, execution varies)
aws athena start-query-execution \
    --query-string "CREATE OR REPLACE VIEW sales_with_customer_details AS SELECT s.sale_id, s.product, s.quantity, s.price, s.sale_date, s.region, c.first_name, c.last_name, c.email, c.country FROM lab20_analytics_db_cli.processed_sales s JOIN lab20_analytics_db_cli.processed_customers c ON s.customer_id = c.customer_id;" \
    --result-configuration "OutputLocation=s3://lab20-data-lake-cli-[RANDOM_STRING]/athena-results/" \
    --region eu-west-1
```

## 🔄 Step 9: Create a Scheduled Workflow

```bash
# Create a trigger for ETL jobs
aws glue create-trigger \
    --name lab20_daily_processing_cli \
    --type SCHEDULED \
    --schedule "cron(0 0 * * ? *)" \
    --actions '[
        {"JobName": "lab20_sales_to_parquet_cli"},
        {"JobName": "lab20_customers_to_parquet_cli"}
    ]' \
    --start-on-creation \
    --region eu-west-1
```

## 🗑️ Step 10: Clean Up Resources (CLI)

```bash
# Delete Athena views (execute query to drop views)
aws athena start-query-execution \
    --query-string "DROP VIEW IF EXISTS sales_with_customer_details;" \
    --result-configuration "OutputLocation=s3://lab20-data-lake-cli-[RANDOM_STRING]/athena-results/" \
    --region eu-west-1

# Delete Glue triggers
aws glue delete-trigger \
    --name lab20_daily_processing_cli \
    --region eu-west-1

# Delete Glue jobs
aws glue delete-job \
    --job-name lab20_sales_to_parquet_cli \
    --region eu-west-1

aws glue delete-job \
    --job-name lab20_customers_to_parquet_cli \
    --region eu-west-1

# Delete Glue crawlers
aws glue delete-crawler \
    --name lab20_customers_crawler_cli \
    --region eu-west-1

aws glue delete-crawler \
    --name lab20_sales_crawler_cli \
    --region eu-west-1

aws glue delete-crawler \
    --name lab20_processed_sales_crawler_cli \
    --region eu-west-1

aws glue delete-crawler \
    --name lab20_processed_customers_crawler_cli \
    --region eu-west-1

# Get table names and delete them
for table in $(aws glue get-tables --database-name lab20_analytics_db_cli --query 'TableList[].Name' --output text --region eu-west-1); do
    aws glue delete-table \
        --database-name lab20_analytics_db_cli \
        --name "$table" \
        --region eu-west-1
done

# Delete Glue database
aws glue delete-database \
    --name lab20_analytics_db_cli \
    --region eu-west-1

# Empty and delete S3 bucket
aws s3 rm s3://lab20-data-lake-cli-[RANDOM_STRING] --recursive
aws s3 rb s3://lab20-data-lake-cli-[RANDOM_STRING] --force

# Delete IAM role
aws iam detach-role-policy \
    --role-name AWSGlueServiceRole-lab20-cli \
    --policy-arn arn:aws:iam::aws:policy/service-role/AWSGlueServiceRole

aws iam detach-role-policy \
    --role-name AWSGlueServiceRole-lab20-cli \
    --policy-arn arn:aws:iam::aws:policy/AmazonS3FullAccess

aws iam delete-role \
    --role-name AWSGlueServiceRole-lab20-cli
```

---

# 🧠 Key Learnings

In this lab, you've learned:

1. How to create and structure a data lake in Amazon S3 with raw and processed data zones
2. How to use AWS Glue crawlers to automatically discover and catalog data
3. How to transform data into optimized formats (Parquet) using AWS Glue ETL jobs
4. How to query and analyze data using Amazon Athena's SQL capabilities
5. How to implement partitioning for better performance and cost optimization
6. How to schedule automated workflows for regular data processing
7. How to implement a complete serverless data analytics solution
8. Best practices for managing and organizing data in a data lake

By applying these techniques in your own projects, you can build scalable, cost-efficient data analytics solutions without having to manage servers or clusters.

---

# 📚 Additional Resources

- [Amazon Athena Documentation](https://docs.aws.amazon.com/athena/latest/ug/what-is.html)
- [AWS Glue Documentation](https://docs.aws.amazon.com/glue/latest/dg/what-is-glue.html)
- [AWS Data Lake Best Practices](https://docs.aws.amazon.com/prescriptive-guidance/latest/defining-data-lake-requirements/welcome.html)
- [Optimizing Amazon Athena Queries](https://docs.aws.amazon.com/athena/latest/ug/performance-tuning.html)
- [AWS Glue ETL Programming Guide](https://docs.aws.amazon.com/glue/latest/dg/programming-guide.html)
- [Building a Data Lake Foundation on AWS](https://aws.amazon.com/blogs/big-data/building-a-data-lake-foundation-for-aws-services-with-aws-lake-formation/) 