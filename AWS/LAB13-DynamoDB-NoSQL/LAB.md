# LAB13: Working with NoSQL Data Using Amazon DynamoDB (Console & CLI)

## ⚡ Lab Overview

In this advanced lab, you'll work with **Amazon DynamoDB**, AWS's fully managed NoSQL database service. You'll learn how to design, create, and interact with DynamoDB tables that scale seamlessly with your application needs. This lab focuses on key NoSQL database concepts and operations, teaching you how to model data efficiently for document and key-value access patterns, and how to perform various database operations using both the AWS Console and CLI.

### 💼 Real-World DevOps Applications

DynamoDB is a cornerstone of many modern cloud-native applications, offering several key advantages:

- **Performance**: Consistent single-digit millisecond response times at any scale
- **Scalability**: Automatic scaling with no downtime or performance degradation
- **Availability**: Multi-AZ replication for high availability (99.999%)
- **Serverless**: No servers to provision, patch, or manage
- **Flexible Schema**: No fixed schema, allowing for agile application development

Common production use cases include:

- **User Profiles**: Storing user data for fast retrieval
- **Session Management**: Handling web or mobile application sessions
- **Product Catalogs**: Managing product information for e-commerce
- **Real-time Analytics**: Storing and querying time-series data
- **Gaming Leaderboards**: Managing high-write scenarios with atomic counters
- **IoT Device Data**: Storing and querying telemetry from connected devices

---

## 🎯 Objectives

By the end of this lab, you will be able to:
- Design appropriate NoSQL data models with effective key structures
- Create DynamoDB tables with partition and sort keys
- Add secondary indexes to enable flexible query patterns
- Perform CRUD operations (Create, Read, Update, Delete) on DynamoDB items
- Execute queries and scans efficiently
- Configure capacity modes for optimal performance and cost
- Understand DynamoDB best practices for real-world applications

---

## 🛠️ Prerequisites

- AWS account with administrative permissions
- Basic understanding of database concepts
- Familiarity with JSON for data representation
- AWS CLI installed and configured (refer to [CLI Setup Guide](../../CLI-Setup.md))

---

# 🌐 PART 1: AWS MANAGEMENT CONSOLE IMPLEMENTATION

In this first part of the lab, you'll create and interact with DynamoDB resources using the AWS Management Console.

## 📊 Step 1: Create DynamoDB Tables (Console)

In this lab, we'll create an e-commerce data model with tables for Products, Orders, and Customers.

### Create the Products Table

1. Sign in to the AWS Management Console
2. Navigate to the **Amazon DynamoDB** service
3. Click **Create table**
4. For **Table name**, enter `lab13-products`
5. For **Partition key**, enter `product_id` with type `String`
6. Leave **Sort key** blank (we'll use a simple primary key for this table)
7. Under **Table settings**, select **Customize settings**
8. For **Read/write capacity settings**, select **Provisioned**
   - Read capacity units: `5`
   - Write capacity units: `5`
9. Leave all other settings as default
10. Click **Create table**

### Create the Orders Table

1. Click **Create table**
2. For **Table name**, enter `lab13-orders`
3. For **Partition key**, enter `customer_id` with type `String`
4. For **Sort key**, enter `order_date` with type `String` (we'll use a composite primary key)
5. Under **Table settings**, select **Customize settings**
6. For **Read/write capacity settings**, select **Provisioned**
   - Read capacity units: `5`
   - Write capacity units: `5`
7. Under **Secondary indexes**, click **Create global index**
   - For **Partition key**, enter `order_id` with type `String`
   - For **Index name**, enter `OrderIdIndex`
   - For **Attribute projections**, select **All**
8. Click **Create index**
9. Leave all other settings as default
10. Click **Create table**

### Create the Customers Table

1. Click **Create table**
2. For **Table name**, enter `lab13-customers`
3. For **Partition key**, enter `customer_id` with type `String`
4. Leave **Sort key** blank
5. Under **Table settings**, select **Customize settings**
6. For **Read/write capacity settings**, select **On-demand** (to show an alternative capacity mode)
7. Under **Secondary indexes**, click **Create global index**
   - For **Partition key**, enter `email` with type `String`
   - For **Index name**, enter `EmailIndex`
   - For **Attribute projections**, select **All**
8. Click **Create index**
9. Leave all other settings as default
10. Click **Create table**

## 🔄 Step 2: Add Data to Tables (Console)

### Add Items to the Products Table

1. Navigate to the **Tables** section and select the `lab13-products` table
2. Click the **Explore table items** button
3. Click **Create item**
4. Enter the following attribute values:
   - `product_id` (String): `P001`
   - Click **Add new attribute** → **String**
   - Enter attribute name: `product_name` and value: `Gaming Laptop`
   - Click **Add new attribute** → **Number**
   - Enter attribute name: `price` and value: `1299.99`
   - Click **Add new attribute** → **String**
   - Enter attribute name: `category` and value: `Electronics`
5. Click **Create item**
6. Repeat to create a few more product items:

**Product 2:**
- `product_id`: `P002`
- `product_name`: `Wireless Headphones`
- `price`: `199.99`
- `category`: `Electronics`

**Product 3:**
- `product_id`: `P003`
- `product_name`: `Ergonomic Office Chair`
- `price`: `249.99`
- `category`: `Furniture`

**Product 4:**
- `product_id`: `P004`
- `product_name`: `Coffee Maker`
- `price`: `89.99`
- `category`: `Kitchen`

### Add Items to the Customers Table

1. Navigate to the `lab13-customers` table
2. Click **Explore table items**
3. Click **Create item**
4. Enter the following attribute values:
   - `customer_id` (String): `C001`
   - Add attribute: `name` (String): `John Smith`
   - Add attribute: `email` (String): `john.smith@example.com`
   - Add attribute: `address` (String): `123 Main St, Anytown, USA`
5. Click **Create item**
6. Repeat to create one more customer:

**Customer 2:**
- `customer_id`: `C002`
- `name`: `Jane Doe`
- `email`: `jane.doe@example.com`
- `address`: `456 Oak Ave, Somewhere, USA`

### Add Items to the Orders Table

1. Navigate to the `lab13-orders` table
2. Click **Explore table items**
3. Click **Create item**
4. Enter the following attribute values:
   - `customer_id` (String): `C001`
   - `order_date` (String): `2023-09-01`
   - Add attribute: `order_id` (String): `O001`
   - Add attribute: `order_status` (String): `Delivered`
   - Add attribute: `total` (Number): `1499.98`
   - Add attribute **List**: `items`
     - Add list elements (each as a Document type):
       - First item: `{"product_id": "P001", "quantity": 1, "price": 1299.99}`
       - Second item: `{"product_id": "P002", "quantity": 1, "price": 199.99}`
5. Click **Create item**
6. Create another order:

**Order 2:**
- `customer_id`: `C002`
- `order_date`: `2023-09-05`
- `order_id`: `O002`
- `order_status`: `Processing`
- `total`: `339.98`
- `items` (List):
  - `{"product_id": "P002", "quantity": 1, "price": 199.99}`
  - `{"product_id": "P004", "quantity": 1, "price": 89.99}`
  - `{"product_id": "P004", "quantity": 1, "price": 50.00}` (as an accessory)

## 🔍 Step 3: Query and Scan Data (Console)

### Query the Orders Table by Customer

1. Navigate to the `lab13-orders` table
2. Click **Explore table items**
3. In the search section, change from **Scan** to **Query**
4. For **Partition key**, enter `C001` (John Smith's customer ID)
5. Click **Run**
6. You should see John's order displayed

### Query Using the Global Secondary Index

1. Still in the `lab13-orders` table query interface
2. Click **Clear query**
3. From the index dropdown, select **OrderIdIndex**
4. For **Partition key**, enter `O002` (Jane's order)
5. Click **Run**
6. You should see Jane's order displayed

### Scan the Products Table with a Filter

1. Navigate to the `lab13-products` table
2. Click **Explore table items**
3. In the search section, select **Scan**
4. Click **Add filter**
5. Set up the filter:
   - Attribute name: `category`
   - Condition: `=`
   - Value: `Electronics`
6. Click **Run**
7. You should see only electronic products (Gaming Laptop and Wireless Headphones)

## 🔄 Step 4: Update and Delete Items (Console)

### Update a Product Item

1. Navigate to the `lab13-products` table
2. Click **Explore table items**
3. Find and select the item with `product_id` = `P001` (Gaming Laptop)
4. Click **Edit item**
5. Find the `price` attribute and change the value from `1299.99` to `1199.99` (a discount!)
6. Add a new attribute **String** called `on_sale` with value `Yes`
7. Click **Save changes**

### Delete an Item

1. Navigate to the `lab13-products` table
2. Click **Explore table items**
3. Find and select the item with `product_id` = `P004` (Coffee Maker)
4. Click **Actions** → **Delete item**
5. Click **Delete** to confirm

## 🧹 Step 5: Console Implementation Cleanup

To avoid ongoing charges, clean up the resources created in the console part of this lab:

1. Navigate to the **DynamoDB** service dashboard
2. In the navigation pane, choose **Tables**
3. Select the `lab13-products` table
4. Click **Delete**
5. Type `delete` in the confirmation field
6. Click **Delete table**
7. Repeat steps 3-6 for the `lab13-orders` table
8. Repeat steps 3-6 for the `lab13-customers` table

---

# 💻 PART 2: AWS CLI IMPLEMENTATION

In this second part of the lab, you'll create and interact with DynamoDB using the AWS Command Line Interface (CLI).

## 📊 Step 1: Create DynamoDB Tables (CLI)

```bash
# Create the Products table with a simple primary key
aws dynamodb create-table \
  --table-name lab13-products \
  --attribute-definitions \
    AttributeName=product_id,AttributeType=S \
  --key-schema \
    AttributeName=product_id,KeyType=HASH \
  --provisioned-throughput ReadCapacityUnits=5,WriteCapacityUnits=5 \
  --region eu-west-1

# Create the Orders table with a composite primary key and global secondary index
aws dynamodb create-table \
  --table-name lab13-orders \
  --attribute-definitions \
    AttributeName=customer_id,AttributeType=S \
    AttributeName=order_date,AttributeType=S \
    AttributeName=order_id,AttributeType=S \
  --key-schema \
    AttributeName=customer_id,KeyType=HASH \
    AttributeName=order_date,KeyType=RANGE \
  --provisioned-throughput ReadCapacityUnits=5,WriteCapacityUnits=5 \
  --global-secondary-indexes \
    "IndexName=OrderIdIndex,\
    KeySchema=[{AttributeName=order_id,KeyType=HASH}],\
    Projection={ProjectionType=ALL},\
    ProvisionedThroughput={ReadCapacityUnits=5,WriteCapacityUnits=5}" \
  --region eu-west-1

# Create the Customers table with a simple primary key and global secondary index
aws dynamodb create-table \
  --table-name lab13-customers \
  --attribute-definitions \
    AttributeName=customer_id,AttributeType=S \
    AttributeName=email,AttributeType=S \
  --key-schema \
    AttributeName=customer_id,KeyType=HASH \
  --billing-mode PAY_PER_REQUEST \
  --global-secondary-indexes \
    "IndexName=EmailIndex,\
    KeySchema=[{AttributeName=email,KeyType=HASH}],\
    Projection={ProjectionType=ALL}" \
  --region eu-west-1

# Wait for the tables to be created (Active status)
echo "Waiting for tables to be created..."
aws dynamodb wait table-exists --table-name lab13-products --region eu-west-1
aws dynamodb wait table-exists --table-name lab13-orders --region eu-west-1
aws dynamodb wait table-exists --table-name lab13-customers --region eu-west-1
echo "All tables are now created and active!"
```

## 🔄 Step 2: Add Data to Tables (CLI)

```bash
# Add items to the Products table
aws dynamodb put-item \
  --table-name lab13-products \
  --item '{
    "product_id": {"S": "P001"},
    "product_name": {"S": "Gaming Laptop"},
    "price": {"N": "1299.99"},
    "category": {"S": "Electronics"}
  }' \
  --region eu-west-1

aws dynamodb put-item \
  --table-name lab13-products \
  --item '{
    "product_id": {"S": "P002"},
    "product_name": {"S": "Wireless Headphones"},
    "price": {"N": "199.99"},
    "category": {"S": "Electronics"}
  }' \
  --region eu-west-1

aws dynamodb put-item \
  --table-name lab13-products \
  --item '{
    "product_id": {"S": "P003"},
    "product_name": {"S": "Ergonomic Office Chair"},
    "price": {"N": "249.99"},
    "category": {"S": "Furniture"}
  }' \
  --region eu-west-1

aws dynamodb put-item \
  --table-name lab13-products \
  --item '{
    "product_id": {"S": "P004"},
    "product_name": {"S": "Coffee Maker"},
    "price": {"N": "89.99"},
    "category": {"S": "Kitchen"}
  }' \
  --region eu-west-1

# Add items to the Customers table
aws dynamodb put-item \
  --table-name lab13-customers \
  --item '{
    "customer_id": {"S": "C001"},
    "name": {"S": "John Smith"},
    "email": {"S": "john.smith@example.com"},
    "address": {"S": "123 Main St, Anytown, USA"}
  }' \
  --region eu-west-1

aws dynamodb put-item \
  --table-name lab13-customers \
  --item '{
    "customer_id": {"S": "C002"},
    "name": {"S": "Jane Doe"},
    "email": {"S": "jane.doe@example.com"},
    "address": {"S": "456 Oak Ave, Somewhere, USA"}
  }' \
  --region eu-west-1

# Add items to the Orders table (with complex nested attributes)
aws dynamodb put-item \
  --table-name lab13-orders \
  --item '{
    "customer_id": {"S": "C001"},
    "order_date": {"S": "2023-09-01"},
    "order_id": {"S": "O001"},
    "order_status": {"S": "Delivered"},
    "total": {"N": "1499.98"},
    "items": {"L": [
      {"M": {
        "product_id": {"S": "P001"},
        "quantity": {"N": "1"},
        "price": {"N": "1299.99"}
      }},
      {"M": {
        "product_id": {"S": "P002"},
        "quantity": {"N": "1"},
        "price": {"N": "199.99"}
      }}
    ]}
  }' \
  --region eu-west-1

aws dynamodb put-item \
  --table-name lab13-orders \
  --item '{
    "customer_id": {"S": "C002"},
    "order_date": {"S": "2023-09-05"},
    "order_id": {"S": "O002"},
    "order_status": {"S": "Processing"},
    "total": {"N": "339.98"},
    "items": {"L": [
      {"M": {
        "product_id": {"S": "P002"},
        "quantity": {"N": "1"},
        "price": {"N": "199.99"}
      }},
      {"M": {
        "product_id": {"S": "P004"},
        "quantity": {"N": "1"},
        "price": {"N": "89.99"}
      }},
      {"M": {
        "product_id": {"S": "P004"},
        "quantity": {"N": "1"},
        "price": {"N": "50.00"}
      }}
    ]}
  }' \
  --region eu-west-1
```

## 🔍 Step 3: Query and Scan Data (CLI)

```bash
# Query Orders table by Customer ID
aws dynamodb query \
  --table-name lab13-orders \
  --key-condition-expression "customer_id = :customer_id" \
  --expression-attribute-values '{":customer_id": {"S": "C001"}}' \
  --region eu-west-1

# Query Orders table using Global Secondary Index
aws dynamodb query \
  --table-name lab13-orders \
  --index-name OrderIdIndex \
  --key-condition-expression "order_id = :order_id" \
  --expression-attribute-values '{":order_id": {"S": "O002"}}' \
  --region eu-west-1

# Scan Products table with filter
aws dynamodb scan \
  --table-name lab13-products \
  --filter-expression "category = :category" \
  --expression-attribute-values '{":category": {"S": "Electronics"}}' \
  --region eu-west-1

# Get a specific customer by email using GSI
aws dynamodb query \
  --table-name lab13-customers \
  --index-name EmailIndex \
  --key-condition-expression "email = :email" \
  --expression-attribute-values '{":email": {"S": "jane.doe@example.com"}}' \
  --region eu-west-1
```

## 🔄 Step 4: Update and Delete Items (CLI)

```bash
# Update a product (apply a discount and add an on_sale attribute)
aws dynamodb update-item \
  --table-name lab13-products \
  --key '{"product_id": {"S": "P001"}}' \
  --update-expression "SET price = :new_price, on_sale = :sale_status" \
  --expression-attribute-values '{
    ":new_price": {"N": "1199.99"},
    ":sale_status": {"S": "Yes"}
  }' \
  --return-values ALL_NEW \
  --region eu-west-1

# Conditional update - only change order status if it's currently "Processing"
aws dynamodb update-item \
  --table-name lab13-orders \
  --key '{
    "customer_id": {"S": "C002"},
    "order_date": {"S": "2023-09-05"}
  }' \
  --update-expression "SET order_status = :new_status" \
  --condition-expression "order_status = :current_status" \
  --expression-attribute-values '{
    ":new_status": {"S": "Shipped"},
    ":current_status": {"S": "Processing"}
  }' \
  --return-values ALL_NEW \
  --region eu-west-1

# Delete an item
aws dynamodb delete-item \
  --table-name lab13-products \
  --key '{"product_id": {"S": "P004"}}' \
  --region eu-west-1

# Verify the item was deleted
aws dynamodb get-item \
  --table-name lab13-products \
  --key '{"product_id": {"S": "P004"}}' \
  --region eu-west-1
```

## 🧪 Step 5: Advanced DynamoDB Operations (CLI)

```bash
# Batch write items (add multiple products at once)
cat > batch-items.json << EOL
{
  "lab13-products": [
    {
      "PutRequest": {
        "Item": {
          "product_id": {"S": "P005"},
          "product_name": {"S": "Smart Watch"},
          "price": {"N": "299.99"},
          "category": {"S": "Electronics"}
        }
      }
    },
    {
      "PutRequest": {
        "Item": {
          "product_id": {"S": "P006"},
          "product_name": {"S": "Blender"},
          "price": {"N": "79.99"},
          "category": {"S": "Kitchen"}
        }
      }
    }
  ]
}
EOL

aws dynamodb batch-write-item \
  --request-items file://batch-items.json \
  --region eu-west-1

# Batch get items (retrieve multiple products at once)
cat > batch-get.json << EOL
{
  "lab13-products": {
    "Keys": [
      {
        "product_id": {"S": "P005"}
      },
      {
        "product_id": {"S": "P006"}
      }
    ]
  }
}
EOL

aws dynamodb batch-get-item \
  --request-items file://batch-get.json \
  --region eu-west-1

# Perform a transaction (all-or-nothing operations)
cat > transaction.json << EOL
{
  "TransactItems": [
    {
      "Update": {
        "TableName": "lab13-products",
        "Key": {
          "product_id": {"S": "P005"}
        },
        "UpdateExpression": "SET price = :price",
        "ExpressionAttributeValues": {
          ":price": {"N": "279.99"}
        }
      }
    },
    {
      "Put": {
        "TableName": "lab13-products",
        "Item": {
          "product_id": {"S": "P007"},
          "product_name": {"S": "Wireless Keyboard"},
          "price": {"N": "59.99"},
          "category": {"S": "Electronics"}
        }
      }
    }
  ]
}
EOL

aws dynamodb transact-write-items \
  --transact-items file://transaction.json \
  --region eu-west-1
```

## 🧹 Step 6: CLI Implementation Cleanup

To avoid ongoing charges, clean up the resources created in the CLI part of this lab:

```bash
# Delete the tables
aws dynamodb delete-table --table-name lab13-products --region eu-west-1
aws dynamodb delete-table --table-name lab13-orders --region eu-west-1
aws dynamodb delete-table --table-name lab13-customers --region eu-west-1

# Delete temporary files
rm -f batch-items.json batch-get.json transaction.json

echo "Cleanup complete"
```

---

## 🧪 Optional Challenges

To deepen your understanding of Amazon DynamoDB, try these additional challenges:

1. **Time-to-Live (TTL)**: Configure TTL on a table to automatically expire and delete items
2. **Capacity Scaling**: Set up auto-scaling for a table's provisioned capacity
3. **Streams**: Enable DynamoDB Streams to capture table changes
4. **Global Tables**: Create a global table replicated across two regions
5. **Data Modeling**: Design a more complex data model for a social media application

---

## 🎓 Summary

In this lab, you've learned how to:
- Create DynamoDB tables with appropriate primary key design
- Configure global secondary indexes for flexible query patterns
- Insert, update, and delete items in DynamoDB tables
- Perform efficient queries and scans with various conditions
- Use batch operations and transactions for complex scenarios
- Manage and clean up DynamoDB resources

These skills form the foundation for designing and implementing NoSQL databases in AWS. DynamoDB's flexibility and performance make it an excellent choice for many modern application scenarios that require scalable, high-performance data storage with flexible schemas. 