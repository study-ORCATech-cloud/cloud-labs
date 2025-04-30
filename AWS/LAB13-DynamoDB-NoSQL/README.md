# AWS LAB13: Resource Overview – Amazon DynamoDB NoSQL Database

## 🗄️ What Resources Are We Working With?

In **AWS LAB13**, you'll explore **NoSQL database operations** using **Amazon DynamoDB**. This lab teaches you how to design, create, and interact with a fully managed NoSQL database that provides fast, predictable performance with seamless scalability, ideal for applications that need consistent single-digit millisecond response times.

---

## 📊 Amazon DynamoDB

### Description
DynamoDB is a key-value and document database that delivers single-digit millisecond performance at any scale, with built-in security, backup and restore, and in-memory caching.

### Key Concepts
- **Tables**: Collections of items (similar to rows in relational databases)
- **Items**: Groups of attributes (similar to records)
- **Attributes**: Fundamental data elements (similar to fields)
- **Primary Key**: Uniquely identifies each item (either simple or composite)
- **Secondary Indexes**: Alternative query paths to improve query flexibility
- **Read/Write Capacity**: Provisioned or on-demand throughput for operations

### What You Do in the Lab
- Create DynamoDB tables with appropriate key structures
- Insert, update, and delete data items
- Query and scan tables efficiently
- Configure indexes for optimal performance
- Explore provisioned vs. on-demand capacity modes

---

## 🔎 Primary Key Types

### Description
DynamoDB offers two types of primary keys that determine how data is organized and accessed.

### Key Concepts
- **Partition Key (Hash Key)**: Determines the partition where data is stored
- **Sort Key (Range Key)**: Optional, enables range queries within a partition
- **Composite Key**: Combination of partition key and sort key

### What You Do in the Lab
- Design appropriate primary key structures
- Understand the impact of partition key choice on performance
- Implement composite keys for hierarchical data relationships

---

## 🔍 Secondary Indexes

### Description
Secondary indexes allow you to query the data in the table using an alternate key, in addition to queries against the primary key.

### Key Concepts
- **Global Secondary Index (GSI)**: Index with a partition key different from the table's
- **Local Secondary Index (LSI)**: Index with the same partition key but different sort key
- **Projected Attributes**: Fields copied into the index for efficient retrieval

### What You Do in the Lab
- Create global and local secondary indexes
- Query tables using secondary indexes
- Configure attribute projections for indexes

---

## 🔄 DynamoDB Operations

### Description
DynamoDB provides various operations for creating, reading, updating, and deleting data, as well as for managing tables.

### Key Concepts
- **CRUD Operations**: Create, Read, Update, Delete operations on items
- **Batch Operations**: Process multiple items in a single request
- **Conditional Writes**: Update or delete items only if conditions are met
- **Transactions**: Group multiple operations into all-or-nothing operations

### What You Do in the Lab
- Perform CRUD operations on items
- Execute batch operations for efficiency
- Implement conditional write operations
- Work with transactions for data consistency

---

## ✅ Summary

| Resource                | Purpose                                             | Created Using    |
|-------------------------|-----------------------------------------------------|------------------|
| DynamoDB Table          | Store and manage NoSQL data                         | Console & CLI    |
| Primary Keys            | Uniquely identify and organize items                | Console & CLI    |
| Secondary Indexes       | Enable alternate query patterns                     | Console & CLI    |
| Items & Attributes      | Store actual data in flexible schema                | Console & CLI    |
| Capacity Settings       | Control performance and cost                        | Console & CLI    |

This lab provides hands-on experience with Amazon DynamoDB, teaching you how to design and interact with NoSQL databases to build highly scalable, high-performance applications with flexible schema requirements. 