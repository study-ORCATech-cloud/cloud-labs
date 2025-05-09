# LAB07: Launch a MySQL Database with Amazon RDS (Console & CLI)

## 🗄️ Lab Overview

In this lab, you'll deploy a **MySQL database** using **Amazon RDS**, AWS's managed relational database service. You'll learn how to configure database parameters, enable secure access with a security group, and connect to the database instance using a SQL client. This lab demonstrates how AWS eliminates the operational overhead of database administration while providing enterprise-grade availability, security, and scalability.

### 🏢 Real-World DevOps Use Case

Database management is a critical but time-consuming aspect of application infrastructure. In traditional environments, DBAs spend significant time on:
- Installing and patching database software
- Managing backups and recovery procedures
- Configuring high availability
- Monitoring performance and storage capacity
- Managing security and updates

Amazon RDS automates these operational tasks, allowing DevOps teams to focus on data modeling, query optimization, and application development instead of infrastructure management. This makes RDS a staple technology in modern cloud architecture patterns.

---

## 🎯 Objectives

By the end of this lab, you will be able to:
- Create and configure an Amazon RDS MySQL instance
- Set up proper security and network access controls
- Connect to the database using a MySQL client
- Perform basic database operations (create tables, insert data)
- Monitor database performance using CloudWatch
- Deploy an RDS instance using AWS CLI for automation
- Understand RDS backup and high availability options

---

## 🛠️ Prerequisites

- AWS account with administrative permissions
- Basic understanding of relational databases and SQL
- MySQL client installed locally
  - Windows: [MySQL Workbench](https://dev.mysql.com/downloads/workbench/)
  - macOS: `brew install mysql-client`
  - Linux: `apt-get install mysql-client` or `yum install mysql`
- AWS CLI installed and configured (refer to [CLI Setup Guide](../../CLI-Setup.md))
- Basic knowledge of networking concepts (VPC, subnets, security groups)

---

## 🏗️ Part 1: Create RDS MySQL Instance

### Step 1: Launch an RDS Instance Through the Console

1. Sign in to the AWS Management Console
2. Navigate to **Amazon RDS** service
3. Click **Create database**
4. Configure the database:
   - **Choose a database creation method**: Standard create
   - **Engine type**: MySQL
   - **Version**: MySQL 8.0.xx (latest available)
   - **Templates**: Free tier (for lab purposes)
   - **DB instance identifier**: `lab07-mysql-db`
   - **Credentials Settings**:
     - **Master username**: `admin`
     - **Master password**: `Password1234!` (in production, use a secure password)
     - **Confirm password**: `Password1234!`
   - **DB instance class**: db.t3.micro (or the Free Tier eligible option)
   - **Storage**:
     - **Storage type**: General Purpose SSD (gp2)
     - **Allocated storage**: 20 GiB
     - **Storage autoscaling**: Enable
     - **Maximum storage threshold**: 100 GiB
5. Under **Connectivity**:
   - **Virtual Private Cloud (VPC)**: Default VPC
   - **Subnet group**: Default
   - **Public access**: Yes (Note: In production environments, this should typically be set to No)
   - **VPC security group**: Create new
   - **New VPC security group name**: `rds-mysql-lab-sg`
   - **Availability Zone**: No preference
   - **Database port**: 3306 (MySQL default)
6. Under **Additional configuration**:
   - **Initial database name**: `labdb`
   - **Parameter group**: default
   - **Automated backups**: Enable
   - **Backup retention period**: 7 days
   - **Backup window**: No preference
   - **Monitoring**: Enable Enhanced monitoring
   - **Monitoring Role**: Default
   - **Granularity**: 60 seconds
7. Review all settings and click **Create database**
8. You'll be redirected to the RDS Databases page where you can monitor the creation progress

### Step 2: Wait for Database Availability

1. The database creation process takes approximately 5-10 minutes
2. The status will change from "Creating" to "Available" when ready
3. Note the **Endpoint** and **Port** displayed on the Connectivity & Security tab

### Step 3: Verify Database Connectivity Settings

1. Go back to **RDS > Databases**
2. Select your database instance `lab07-mysql-db`
3. Go to the **Connectivity & security** tab
4. Verify the following:
   - **Endpoint** is displayed (e.g., `lab07-mysql-db.abcdefg.us-east-1.rds.amazonaws.com`)
   - **Port** shows 3306
   - **VPC security groups** shows your security group (`rds-mysql-lab-sg`)
   - **Publicly accessible** is set to Yes
5. Run `telnet` to the database endpoint to make sure it is available:
   ```bash
   telnet <DATABASE-ENDPOINT> 3306
   ```
---

## 🔌 Part 2: Connect to the Database

### Step 1: Prepare Your Connection Parameters

1. Note down your RDS instance endpoint from the RDS console:
   - e.g., `lab07-mysql-db.abcdefg.us-east-1.rds.amazonaws.com`
2. Confirm you have the following connection details:
   - **Hostname**: Your RDS endpoint
   - **Port**: 3306
   - **Username**: admin
   - **Password**: Password1234!
   - **Database**: labdb

### Step 2: Connect Using the MySQL Command Line Client

```bash
# Basic connection
mysql -h lab07-mysql-db.abcdefg.us-east-1.rds.amazonaws.com -u admin -p labdb

# When prompted, enter your password: Password1234!
```

### Step 3: Verify Connection and Perform Basic SQL Operations

Once connected, you should see a MySQL prompt. Let's create a table and insert some data:

```sql
-- Check current database
SELECT DATABASE();

-- Create a customers table
CREATE TABLE customers (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Insert some test data
INSERT INTO customers (name, email) VALUES 
('John Doe', 'john@example.com'),
('Jane Smith', 'jane@example.com'),
('Bob Johnson', 'bob@example.com');

-- Verify the data
SELECT * FROM customers;

-- Show table structure
DESCRIBE customers;
```

### Step 4: (Optional) Connect Using MySQL Workbench

If you're using MySQL Workbench or another GUI client:

1. Open MySQL Workbench
2. Click **+** next to MySQL Connections
3. Enter a Connection Name (e.g., "AWS RDS Lab")
4. Enter the connection details:
   - **Hostname**: Your RDS endpoint
   - **Port**: 3306
   - **Username**: admin
5. Click **Test Connection** to verify connectivity
6. If successful, click **OK** to save the connection
7. Double-click the saved connection to connect to your database
8. Enter your password when prompted

---

## 📈 Part 3: Monitor with CloudWatch

### Step 1: View Basic RDS Metrics

1. Go to **RDS > Databases** in the AWS Console
2. Select your database instance `lab07-mysql-db`
3. Go to the **Monitoring** tab
4. Review the default metrics displayed, including:
   - CPU Utilization
   - Database Connections
   - Free Storage Space
   - Read/Write IOPS
   - Read/Write Latency

### Step 2: Create a CloudWatch Alarm for Storage Space

1. Go to **CloudWatch > Alarms > All alarms** in the AWS Console
2. Click **Create alarm**
3. Click **Select metric**
4. Navigate to **RDS > Per-Database Metrics**
5. Find your DB instance and select `FreeStorageSpace`
6. Click **Select metric**
7. Configure the alarm:
   - **Statistic**: Average
   - **Period**: 5 minutes
   - Under **Conditions**:
     - **Threshold type**: Static
     - **Whenever FreeStorageSpace is...**: Lower than
     - **than...**: 5000 (5 GB)
8. Click **Next**
9. Under **Notification**, select **Create new topic** (or use existing if you have one)
   - **Topic name**: RDS-Storage-Alarm
   - **Email endpoints**: Enter your email address
10. Click **Create topic**
11. Click **Next**
12. Add a name and description:
    - **Alarm name**: Low-Storage-lab07-mysql-db
    - **Alarm description**: Notify when free storage drops below 5GB
13. Click **Next**
14. Review configuration and click **Create alarm**
15. Confirm the SNS topic subscription in your email

### Step 3: View Enhanced Monitoring (Optional)

If you enabled Enhanced Monitoring during database creation:

1. Go to **RDS > Databases** in the AWS Console
2. Select your database instance
3. In the **Monitoring** tab, find the **Enhanced monitoring** section
4. View metrics such as:
   - OS process list
   - Memory usage breakdown
   - Disk I/O detailed metrics

---

## 💻 Part 4: Deploy RDS Using AWS CLI

You can deploy the same resources using AWS CLI for automation:

### Step 1: Create a Security Group

```bash
# Create a security group for RDS
SEC_GROUP_ID=$(aws ec2 create-security-group \
  --group-name rds-mysql-cli-sg \
  --description "Security group for RDS MySQL Lab" \
  --vpc-id vpc-xxxxxxxx \
  --query 'GroupId' \
  --output text)

echo "Created security group: $SEC_GROUP_ID"

# Add a rule to allow MySQL traffic from your IP
MY_IP=$(curl -s https://checkip.amazonaws.com)/32
aws ec2 authorize-security-group-ingress \
  --group-id $SEC_GROUP_ID \
  --protocol tcp \
  --port 3306 \
  --cidr $MY_IP
```

### Step 2: Create a DB Subnet Group

```bash
# If not using default subnet group
aws rds create-db-subnet-group \
  --db-subnet-group-name rds-mysql-subnet-group \
  --db-subnet-group-description "Subnet group for RDS MySQL Lab" \
  --subnet-ids subnet-xxxxxxxx subnet-yyyyyyyy
```

### Step 3: Create RDS Instance

```bash
# Create RDS MySQL instance
aws rds create-db-instance \
  --db-instance-identifier lab07-mysql-cli \
  --db-instance-class db.t3.micro \
  --engine mysql \
  --master-username admin \
  --master-user-password Password1234! \
  --allocated-storage 20 \
  --max-allocated-storage 100 \
  --db-name labdb \
  --vpc-security-group-ids $SEC_GROUP_ID \
  --publicly-accessible \
  --backup-retention-period 7 \
  --monitoring-interval 60 \
  --enable-cloudwatch-logs-exports error

echo "Creating RDS instance. This will take several minutes..."
```

### Step 4: Check Instance Status

```bash
# Get information about the instance (repeat until state is "available")
aws rds describe-db-instances \
  --db-instance-identifier lab07-mysql-cli \
  --query 'DBInstances[0].{Status:DBInstanceStatus,Endpoint:Endpoint.Address}'
```

---

## 🧪 Challenge: Database Management Tasks

Complete these additional tasks to expand your RDS knowledge:

### Challenge 1: Configure Automated Database Snapshots

1. Navigate to your RDS instance in the AWS Console
2. Click **Modify**
3. Under **Backup**, set:
   - **Backup retention period**: 7 days
   - **Backup window**: Select a convenient time
4. Apply the changes immediately
5. Document the steps taken and why automated backups are important

### Challenge 2: Test Multi-AZ Deployment (Note: Additional Costs Apply)

1. Navigate to your RDS instance in the AWS Console
2. Click **Modify**
3. Under **Availability & durability**, select **Create a standby instance**
4. Apply the changes (note this will cause a brief outage)
5. Monitor the modification process
6. Check the instance details to verify Multi-AZ is enabled
7. Research and document the benefits and costs of Multi-AZ deployment

### Challenge 3: Create a Read Replica (Note: Additional Costs Apply)

1. Select your RDS instance
2. From the **Actions** menu, select **Create read replica**
3. Configure a basic read replica and launch it
4. After creation, practice connecting to both the primary and replica instances
5. Document the differences in connection and capabilities

---

## 🧹 Cleanup

To avoid incurring charges, clean up all resources:

### Console Cleanup:

1. Go to **RDS > Databases**
2. Select your database instance (and any replicas if created)
3. From **Actions** menu, select **Delete**
4. Uncheck **Create final snapshot** and **Retain automated backups**
5. Type "delete me" to confirm
6. Click **Delete**
7. Delete the DB Subnet Group if you created one
8. Go to **EC2 > Security Groups**
9. Delete the RDS security group(s) you created

### CLI Cleanup:

```bash
# Delete the RDS instance (this may take several minutes)
aws rds delete-db-instance \
  --db-instance-identifier lab07-mysql-cli \
  --skip-final-snapshot

echo "Deleting RDS instance. This will take several minutes..."

# Wait for the instance to be deleted

# Delete security group
aws ec2 delete-security-group --group-id $SEC_GROUP_ID

# Delete CloudWatch alarm
aws cloudwatch delete-alarms --alarm-names Low-Storage-lab07-mysql-db
```

---

## ✅ Key Takeaways

In this lab, you've learned:

- How to deploy a fully managed MySQL database in the AWS cloud
- The difference between manual database management and managed RDS
- Security best practices for database access control
- How to connect to and manage a MySQL database
- Database monitoring and alerting with CloudWatch
- Automation of database deployment using AWS CLI
- The importance of backups, high availability, and read replicas

These skills are foundational for database management in the AWS cloud and represent common tasks for cloud and DevOps engineers.

## 📚 Additional Resources

- [Amazon RDS User Guide](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Welcome.html)
- [RDS for MySQL Documentation](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_MySQL.html)
- [MySQL Documentation](https://dev.mysql.com/doc/)
- [RDS Best Practices](https://aws.amazon.com/blogs/database/best-practices-for-amazon-rds/)
- [RDS Pricing](https://aws.amazon.com/rds/mysql/pricing/)

---

Happy Database Management! 🗄️

