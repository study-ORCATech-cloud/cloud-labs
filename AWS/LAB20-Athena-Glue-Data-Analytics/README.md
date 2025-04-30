# AWS LAB20: Resource Overview – Amazon Athena and AWS Glue Data Analytics

## 🗄️ What Resources Are We Working With?

In **AWS LAB20**, you'll explore **serverless data analytics** using **Amazon Athena and AWS Glue**. This lab teaches you how to build an end-to-end data analytics solution that crawls, catalogs, transforms, and queries your data stored in Amazon S3 without managing any infrastructure.

---

## 📊 Amazon Athena

### Description
Amazon Athena is an interactive query service that makes it easy to analyze data directly in Amazon S3 using standard SQL. Athena is serverless, so there is no infrastructure to set up or manage, and you pay only for the queries you run.

### Key Concepts
- **SQL Engine**: Built on Presto, a distributed SQL query engine
- **Serverless Architecture**: No clusters or infrastructure to manage
- **Schema-on-Read**: Query data in its original format without transformation
- **Pay-per-Query**: Pay only for the data scanned by each query
- **Query Federation**: Query data across different sources (S3, RDS, etc.)
- **Data Formats**: Support for CSV, JSON, Parquet, ORC, Avro, and more
- **Integration**: Seamless integration with Glue Data Catalog and other AWS services

### What You Do in the Lab
- Create and configure Athena workgroups and query settings
- Write and execute SQL queries against data in S3
- Analyze query performance and optimize for cost
- Export query results to various formats
- Set up and use Athena with different data formats
- Create and manage saved queries and query history

---

## 🔎 AWS Glue

### Description
AWS Glue is a fully managed extract, transform, and load (ETL) service that makes it easy to prepare and load data for analytics. Glue discovers your data and stores the associated metadata in the AWS Glue Data Catalog, making the data immediately searchable and queryable.

### Key Concepts
- **Data Catalog**: Central metadata repository for all your data assets
- **Crawlers**: Automatically discover and catalog data sources
- **ETL Jobs**: Transform data between source and target
- **Triggers**: Schedule or event-based execution of crawlers and jobs
- **Classifiers**: Determine the schema of your data
- **Data Quality**: Validate and ensure data meets quality standards
- **Development Endpoints**: Interactive development environment for ETL scripts

### What You Do in the Lab
- Create and configure Glue databases and tables
- Set up and run Glue crawlers to discover data
- Build and execute Glue ETL jobs for data transformation
- Work with the Glue Data Catalog for metadata management
- Implement data partitioning for performance
- Create custom classifiers for specialized data formats
- Schedule automated workflows with Glue triggers

---

## 🔄 Athena and Glue Integration

### Description
Integrating Amazon Athena with AWS Glue creates a powerful serverless data analytics platform that enables you to discover, prepare, and analyze your data with minimal setup and management.

### Key Concepts
- **End-to-End Analytics**: Complete data pipeline from source to insights
- **Schema Evolution**: Handle changing data structures over time
- **Partitioning Strategy**: Optimize performance and cost with effective partitioning
- **Format Conversion**: Transform data into analytics-optimized formats
- **Security Model**: Implement fine-grained access controls
- **Cost Management**: Strategies for optimizing analytics costs

### What You Do in the Lab
- Set up a complete serverless data analytics solution
- Create an automated workflow from data ingestion to analysis
- Implement best practices for performance and cost optimization
- Build visualization dashboards with query results
- Design a comprehensive security model for your data
- Develop a strategy for ongoing data management

---

## ✅ Summary

| Resource                | Purpose                                             | Created Using    |
|-------------------------|-----------------------------------------------------|------------------|
| Athena Workgroup        | Isolate query execution and track usage             | Console & CLI    |
| Athena Query            | Execute SQL to analyze data in S3                   | Console & CLI    |
| Glue Database           | Organize and contain tables of metadata             | Console & CLI    |
| Glue Crawler            | Discover schema and update the Data Catalog         | Console & CLI    |
| Glue Table              | Store metadata about your dataset                   | Console & CLI    |
| Glue ETL Job            | Transform and process data between source and target| Console & CLI    |
| Glue Trigger            | Schedule or event-based execution of workflows      | Console & CLI    |
| S3 Bucket               | Store raw and processed data                        | Console & CLI    |

This lab provides hands-on experience with Amazon Athena and AWS Glue, teaching you how to implement a serverless data analytics solution that scales with your data needs without the operational overhead of traditional data warehousing. These services provide the foundation for modern data lakes and analytics platforms in the cloud. 