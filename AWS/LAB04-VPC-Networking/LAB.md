# LAB04: Create a VPC with Subnets and Security Groups (AWS Console & CLI)

## 🌐 Lab Overview

In this lab, you will design and deploy a **custom Virtual Private Cloud (VPC)** in AWS. You'll learn how to segment a network using **public and private subnets**, control access with **security groups**, and enable internet connectivity with a **route table** and **Internet Gateway**. This is a key skill for building secure and scalable cloud architectures.

### 📚 Core Concepts for Beginners

- **What is a VPC?** Think of it as your own private section of the AWS cloud, isolated from other users.
- **Subnets**: Subdivisions of your VPC's IP address range, like neighborhoods in a city.
- **Public vs Private Subnets**: Public subnets can reach the internet directly, private subnets cannot.
- **CIDR Notation**: A way to represent IP address ranges (e.g., 10.0.0.0/16 contains 65,536 IP addresses).
- **Internet Gateway**: The door that connects your VPC to the internet.
- **Route Tables**: Traffic directors that determine where network traffic is sent.
- **Security Groups**: Virtual firewalls that control inbound and outbound traffic at the instance level.

### 🏙️ Real-World Analogy

Think of a VPC as a corporate office building:
- The **VPC** is the entire building
- **Subnets** are the floors of the building
- **Public subnets** are floors with windows and external doors
- **Private subnets** are secure inner rooms with no outside access
- The **Internet Gateway** is the main entrance to the building
- **Route tables** are the signs directing people where to go
- **Security groups** are the key card readers on each door

## 🎯 Objectives

By the end of this lab, you will be able to:

- Create a custom VPC with a specific IP address range
- Create public and private subnets in different Availability Zones
- Launch an EC2 instance into a public subnet
- Configure security groups to control network access
- Create and attach an Internet Gateway
- Configure route tables to direct traffic
- Understand the difference between public and private subnets

## 🛠️ Prerequisites

- AWS account
- AWS CLI installed and configured (refer to the [CLI Setup Guide](../../CLI-Setup.md))
- IAM permissions for VPC, EC2, and networking components

## 🌐 Network Architecture for This Lab

We'll build the following architecture:

```
                   Internet
                       │
                       ▼
                Internet Gateway
                       │
                       ▼
      ┌───────────────────────────────┐
      │         VPC (10.0.0.0/16)     │
      │                               │
      │    ┌─────────────────────┐    │
      │    │  Public Subnet      │    │
      │    │  (10.0.1.0/24)      │    │
      │    │  AZ: eu-west-1a     │    │
      │    │                     │    │
      │    │  ┌─────────────┐    │    │
      │    │  │ EC2 Instance│    │    │
      │    │  └─────────────┘    │    │
      │    └─────────────────────┘    │
      │                               │
      │    ┌─────────────────────┐    │
      │    │  Private Subnet     │    │
      │    │  (10.0.2.0/24)      │    │
      │    │  AZ: eu-west-1b     │    │
      │    └─────────────────────┘    │
      └───────────────────────────────┘
```

## 🖥️ Part 1: Create a VPC and Subnets (Console)

### Step 1: Create a VPC

1. Log in to the [AWS Management Console](https://console.aws.amazon.com)
2. Search for "VPC" in the search bar and select the VPC service
3. On the VPC Dashboard, click **Create VPC**
4. Choose **VPC only** option
5. Fill in the details:
   - **Name tag**: `lab04-vpc`
   - **IPv4 CIDR block**: Enter `10.0.0.0/16` (provides 65,536 IP addresses)
   - **Tenancy**: Leave as "Default"
   - **Tags**: Optionally add tags (e.g., Key: "Purpose", Value: "Training")
6. Click **Create VPC**
7. You'll see a success message and your VPC will appear in the VPC list

### Step 2: Create Subnets

Now we'll create two subnets within our VPC: one public and one private.

#### Create the Public Subnet:

1. In the left navigation pane, click on **Subnets**
2. Click **Create subnet**
3. Fill in the details:
   - **VPC ID**: Select the `lab04-vpc` you created
   - **Subnet name**: `lab04-public-subnet`
   - **Availability Zone**: Select `eu-west-1a` (Ireland, first AZ)
   - **IPv4 CIDR block**: `10.0.1.0/24` (provides 256 IP addresses)
4. Click **Create subnet**

#### Create the Private Subnet:

1. Click **Create subnet** again
2. Fill in the details:
   - **VPC ID**: Select the `lab04-vpc` again
   - **Subnet name**: `lab04-private-subnet`
   - **Availability Zone**: Select `eu-west-1b` (different from the public subnet for high availability)
   - **IPv4 CIDR block**: `10.0.2.0/24` (provides 256 IP addresses)
3. Click **Create subnet**

### Step 3: Enable Auto-Assign Public IP for Public Subnet

For the public subnet to automatically assign public IPs to instances:

1. Select the `lab04-public-subnet` from your subnet list
2. Click **Actions** > **Edit subnet settings**
3. Check the box for **Enable auto-assign public IPv4 address**
4. Click **Save**

### Step 4: Create an Internet Gateway

An Internet Gateway (IGW) allows communication between your VPC and the internet.

1. In the left navigation pane, click on **Internet Gateways**
2. Click **Create internet gateway**
3. **Name tag**: `lab04-igw`
4. Click **Create internet gateway**
5. After it's created, click **Actions** > **Attach to VPC**
6. Select your `lab04-vpc` from the dropdown
7. Click **Attach internet gateway**

### Step 5: Create and Configure Route Tables

Now we'll create a route table for the public subnet that routes internet traffic through the IGW.

1. In the left navigation pane, click on **Route Tables**
2. Click **Create route table**
3. Fill in:
   - **Name**: `lab04-public-rt`
   - **VPC**: Select your `lab04-vpc`
4. Click **Create route table**

#### Add Internet Route:

1. Select the route table you just created
2. Go to the **Routes** tab
3. Click **Edit routes**
4. Click **Add route**
5. Enter:
   - **Destination**: `0.0.0.0/0` (All IPv4 traffic)
   - **Target**: Select "Internet Gateway" and choose your `lab04-igw`
6. Click **Save changes**

#### Associate with Public Subnet:

1. Still on your route table, go to the **Subnet associations** tab
2. Click **Edit subnet associations**
3. Select your `lab04-public-subnet`
4. Click **Save associations**

By default, AWS creates a main route table for your VPC that doesn't have internet access. Your private subnet is automatically associated with this main route table, so it remains private.

## 🔐 Part 2: Create a Security Group and Launch an EC2 Instance

### Step 1: Create a Security Group

1. In the left navigation pane, click on **Security Groups**
2. Click **Create security group**
3. Fill in:
   - **Security group name**: `lab04-web-sg`
   - **Description**: `Allow SSH and HTTP access`
   - **VPC**: Select your `lab04-vpc`
4. Under **Inbound rules**, click **Add rule** twice to add these rules:
   - Rule 1:
     - **Type**: SSH (port 22)
     - **Source**: My IP (this automatically adds your current IP address)
     - **Description**: SSH from my IP
   - Rule 2:
     - **Type**: HTTP (port 80)
     - **Source**: 0.0.0.0/0 (anywhere)
     - **Description**: HTTP from anywhere
5. Leave the outbound rules as is (allow all outbound traffic)
6. Click **Create security group**

### Step 2: Launch an EC2 Instance in the Public Subnet

1. Navigate to the EC2 Dashboard (search for "EC2" in the search bar)
2. Click **Launch instance**
3. **Name**: `lab04-web-server`
4. **AMI**: Select Amazon Linux 2 (free tier eligible)
5. **Instance type**: t2.micro (free tier eligible)
6. **Key pair**: Select an existing key pair or create a new one
7. Under **Network settings**:
   - Click **Edit**
   - **VPC**: Select your `lab04-vpc`
   - **Subnet**: Select your `lab04-public-subnet`
   - **Auto-assign public IP**: Enable
   - **Security group**: Select existing, then choose `lab04-web-sg`
8. Configure storage (leave as default)
9. Under **Advanced details** (scroll down), in the User data field, paste the following to install a basic web server:
   ```bash
   #!/bin/bash
   yum update -y
   yum install -y httpd
   systemctl start httpd
   systemctl enable httpd
   echo "<html><body><h1>Hello from my VPC Lab!</h1><p>Server is running in $(curl -s http://169.254.169.254/latest/meta-data/placement/availability-zone)</p></body></html>" > /var/www/html/index.html
   ```
10. Click **Launch instance**

### Step 3: Test Your Setup

1. Wait for the instance to be in the "Running" state
2. Select your instance and note the **Public IPv4 address** from the instance details
3. Test SSH access:
   ```bash
   ssh -i /path/to/your-key.pem ec2-user@YOUR_INSTANCE_PUBLIC_IP
   ```
4. If SSH works, you've confirmed your public subnet and security group are working
5. Open a web browser and navigate to `http://YOUR_INSTANCE_PUBLIC_IP`
6. You should see your web page, confirming HTTP access works

## 💻 Part 3: Create the Same Setup Using AWS CLI

Let's now create the same architecture using the AWS CLI. You can choose to run these commands separately or save them as a script.

### Step 1: Create VPC

```bash
# Create VPC and capture its ID
VPC_ID=$(aws ec2 create-vpc \
  --cidr-block 10.0.0.0/16 \
  --tag-specifications 'ResourceType=vpc,Tags=[{Key=Name,Value=lab04-cli-vpc}]' \
  --query Vpc.VpcId \
  --output text)

echo "VPC created with ID: $VPC_ID"
```

### Step 2: Create Subnets

```bash
# Create public subnet
PUBLIC_SUBNET_ID=$(aws ec2 create-subnet \
  --vpc-id $VPC_ID \
  --cidr-block 10.0.1.0/24 \
  --availability-zone eu-west-1a \
  --tag-specifications 'ResourceType=subnet,Tags=[{Key=Name,Value=lab04-cli-public-subnet}]' \
  --query Subnet.SubnetId \
  --output text)

echo "Public Subnet created with ID: $PUBLIC_SUBNET_ID"

# Create private subnet
PRIVATE_SUBNET_ID=$(aws ec2 create-subnet \
  --vpc-id $VPC_ID \
  --cidr-block 10.0.2.0/24 \
  --availability-zone eu-west-1b \
  --tag-specifications 'ResourceType=subnet,Tags=[{Key=Name,Value=lab04-cli-private-subnet}]' \
  --query Subnet.SubnetId \
  --output text)

echo "Private Subnet created with ID: $PRIVATE_SUBNET_ID"
```

### Step 3: Create and Attach Internet Gateway

```bash
# Create Internet Gateway
IGW_ID=$(aws ec2 create-internet-gateway \
  --tag-specifications 'ResourceType=internet-gateway,Tags=[{Key=Name,Value=lab04-cli-igw}]' \
  --query InternetGateway.InternetGatewayId \
  --output text)

echo "Internet Gateway created with ID: $IGW_ID"

# Attach IGW to VPC
aws ec2 attach-internet-gateway \
  --internet-gateway-id $IGW_ID \
  --vpc-id $VPC_ID

echo "Internet Gateway attached to VPC"
```

### Step 4: Configure Route Tables

```bash
# Create route table for public subnet
PUBLIC_RT_ID=$(aws ec2 create-route-table \
  --vpc-id $VPC_ID \
  --tag-specifications 'ResourceType=route-table,Tags=[{Key=Name,Value=lab04-cli-public-rt}]' \
  --query RouteTable.RouteTableId \
  --output text)

echo "Public Route Table created with ID: $PUBLIC_RT_ID"

# Add route to IGW
aws ec2 create-route \
  --route-table-id $PUBLIC_RT_ID \
  --destination-cidr-block 0.0.0.0/0 \
  --gateway-id $IGW_ID

echo "Route to IGW added to Public Route Table"

# Associate public subnet with the route table
aws ec2 associate-route-table \
  --subnet-id $PUBLIC_SUBNET_ID \
  --route-table-id $PUBLIC_RT_ID

echo "Public Subnet associated with Public Route Table"
```

### Step 5: Create Security Group

```bash
# Create security group
SG_ID=$(aws ec2 create-security-group \
  --group-name lab04-cli-sg \
  --description "Allow SSH and HTTP" \
  --vpc-id $VPC_ID \
  --query GroupId \
  --output text)

echo "Security Group created with ID: $SG_ID"

# Add SSH rule - replace YOUR_IP with your IP address followed by /32
aws ec2 authorize-security-group-ingress \
  --group-id $SG_ID \
  --protocol tcp \
  --port 22 \
  --cidr YOUR_IP/32

echo "SSH rule added to Security Group"

# Add HTTP rule
aws ec2 authorize-security-group-ingress \
  --group-id $SG_ID \
  --protocol tcp \
  --port 80 \
  --cidr 0.0.0.0/0

echo "HTTP rule added to Security Group"
```

### Step 6: Launch EC2 Instance

```bash
# Enable public IP for the subnet
aws ec2 modify-subnet-attribute \
  --subnet-id $PUBLIC_SUBNET_ID \
  --map-public-ip-on-launch

echo "Auto-assign public IP enabled for Public Subnet"

# Get a recent Amazon Linux 2 AMI ID
AMI_ID=$(aws ssm get-parameters \
  --names /aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-gp2 \
  --query 'Parameters[0].Value' \
  --output text)

echo "Using AMI ID: $AMI_ID"

# Launch EC2 instance in public subnet
INSTANCE_ID=$(aws ec2 run-instances \
  --image-id $AMI_ID \
  --instance-type t2.micro \
  --key-name YOUR_KEY_NAME \
  --subnet-id $PUBLIC_SUBNET_ID \
  --security-group-ids $SG_ID \
  --user-data file://userdata.txt \
  --tag-specifications 'ResourceType=instance,Tags=[{Key=Name,Value=lab04-cli-web-server}]' \
  --query Instances[0].InstanceId \
  --output text)

echo "EC2 Instance launched with ID: $INSTANCE_ID"
```

Before running the command above, create a file named `userdata.txt` with the following content:

```bash
#!/bin/bash
yum update -y
yum install -y httpd
systemctl start httpd
systemctl enable httpd
echo "<html><body><h1>Hello from my VPC CLI Lab!</h1><p>Server is running in $(curl -s http://169.254.169.254/latest/meta-data/placement/availability-zone)</p></body></html>" > /var/www/html/index.html
```

## 🧪 Challenge: Enable Internet Access from Private Subnet using NAT Gateway

For advanced learners, try this challenge:

1. Create a NAT Gateway in the public subnet
2. Update the route table for the private subnet to route internet traffic through the NAT Gateway
3. Launch an EC2 instance in the private subnet
4. SSH into the public instance, then SSH from there to the private instance
5. Verify the private instance can access the internet but isn't accessible from the internet

### NAT Gateway CLI Commands:

```bash
# Allocate Elastic IP for NAT Gateway
ALLOCATION_ID=$(aws ec2 allocate-address \
  --domain vpc \
  --query AllocationId \
  --output text)

# Create NAT Gateway in public subnet
NAT_GW_ID=$(aws ec2 create-nat-gateway \
  --subnet-id $PUBLIC_SUBNET_ID \
  --allocation-id $ALLOCATION_ID \
  --tag-specifications 'ResourceType=natgateway,Tags=[{Key=Name,Value=lab04-cli-nat-gw}]' \
  --query NatGateway.NatGatewayId \
  --output text)

# Create private route table
PRIVATE_RT_ID=$(aws ec2 create-route-table \
  --vpc-id $VPC_ID \
  --tag-specifications 'ResourceType=route-table,Tags=[{Key=Name,Value=lab04-cli-private-rt}]' \
  --query RouteTable.RouteTableId \
  --output text)

# Wait for NAT Gateway to be available
aws ec2 wait nat-gateway-available --nat-gateway-ids $NAT_GW_ID

# Add route through NAT Gateway for internet access
aws ec2 create-route \
  --route-table-id $PRIVATE_RT_ID \
  --destination-cidr-block 0.0.0.0/0 \
  --nat-gateway-id $NAT_GW_ID

# Associate private subnet with private route table
aws ec2 associate-route-table \
  --subnet-id $PRIVATE_SUBNET_ID \
  --route-table-id $PRIVATE_RT_ID
```

## 🔍 Troubleshooting Common Issues

### Instance Not Reachable via SSH or HTTP
- Check the Security Group - verify inbound rules allow traffic on port 22 (SSH) and port 80 (HTTP)
- Ensure your instance is in the public subnet
- Verify the route table for the public subnet has the internet gateway route

### Cannot Access Internet from the Public Subnet
- Confirm the Internet Gateway is attached to your VPC
- Ensure the route table has a route to 0.0.0.0/0 targeting your Internet Gateway
- Verify the route table is associated with your public subnet

### Private Instances Cannot Access Internet
- For the NAT Gateway solution, ensure it's in a public subnet
- Check that the private subnet's route table has a route to the NAT Gateway
- Verify the NAT Gateway has a valid Elastic IP

## 🌀 Cleanup - IMPORTANT!

To avoid ongoing charges, clean up all resources:

### Console Cleanup:
1. Terminate any EC2 instances
2. Delete NAT Gateway (if created)
3. Release Elastic IP (if allocated)
4. Detach and delete Internet Gateway
5. Delete Subnets
6. Delete Route Tables (except the main one)
7. Delete Security Groups (except the default one)
8. Finally, delete the VPC

### CLI Cleanup:

```bash
# Get resource IDs (if not stored in variables)
VPC_ID=$(aws ec2 describe-vpcs --filters "Name=tag:Name,Values=lab04-cli-vpc" --query Vpcs[0].VpcId --output text)
IGW_ID=$(aws ec2 describe-internet-gateways --filters "Name=attachment.vpc-id,Values=$VPC_ID" --query InternetGateways[0].InternetGatewayId --output text)
NAT_GW_ID=$(aws ec2 describe-nat-gateways --filter "Name=vpc-id,Values=$VPC_ID" --query NatGateways[0].NatGatewayId --output text)
INSTANCE_ID=$(aws ec2 describe-instances --filters "Name=tag:Name,Values=lab04-cli-web-server" "Name=instance-state-name,Values=running" --query Instances[0].InstanceId --output text)

# Terminate instance
aws ec2 terminate-instances --instance-ids $INSTANCE_ID
aws ec2 wait instance-terminated --instance-ids $INSTANCE_ID

# Delete NAT Gateway
if [ ! -z "$NAT_GW_ID" ]; then
  aws ec2 delete-nat-gateway --nat-gateway-id $NAT_GW_ID
  echo "Waiting for NAT Gateway to be deleted..."
  sleep 60  # NAT Gateway takes time to delete
fi

# Release Elastic IP
ALLOCATION_ID=$(aws ec2 describe-addresses --query Addresses[0].AllocationId --output text)
if [ ! -z "$ALLOCATION_ID" ]; then
  aws ec2 release-address --allocation-id $ALLOCATION_ID
fi

# Detach and delete Internet Gateway
aws ec2 detach-internet-gateway --internet-gateway-id $IGW_ID --vpc-id $VPC_ID
aws ec2 delete-internet-gateway --internet-gateway-id $IGW_ID

# Delete Subnets
for SUBNET_ID in $(aws ec2 describe-subnets --filters "Name=vpc-id,Values=$VPC_ID" --query Subnets[*].SubnetId --output text); do
  aws ec2 delete-subnet --subnet-id $SUBNET_ID
done

# Delete Route Tables (except main)
for RT_ID in $(aws ec2 describe-route-tables --filters "Name=vpc-id,Values=$VPC_ID" "Name=association.main,Values=false" --query RouteTables[*].RouteTableId --output text); do
  aws ec2 delete-route-table --route-table-id $RT_ID
done

# Delete Security Groups (except default)
for SG_ID in $(aws ec2 describe-security-groups --filters "Name=vpc-id,Values=$VPC_ID" "Name=group-name,Values=!=default" --query SecurityGroups[*].GroupId --output text); do
  aws ec2 delete-security-group --group-id $SG_ID
done

# Delete VPC
aws ec2 delete-vpc --vpc-id $VPC_ID
```

## ✅ What You've Learned

Congratulations! In this lab, you have:

1. Created a Virtual Private Cloud (VPC) with custom IP addressing
2. Set up public and private subnets in different Availability Zones
3. Created and attached an Internet Gateway to enable internet access
4. Configured route tables to direct traffic appropriately
5. Created security groups to control network access
6. Launched an EC2 instance in a public subnet with web server functionality
7. Learned how to perform all these operations via both the AWS Console and CLI
8. (Optional) Set up a NAT Gateway to allow private instances to access the internet

These networking skills are essential for designing secure and scalable architectures in AWS. Understanding VPCs, subnets, and routing is foundational knowledge that applies to nearly all AWS solutions.

## 📚 Additional Resources

- [Amazon VPC Documentation](https://docs.aws.amazon.com/vpc/latest/userguide/what-is-amazon-vpc.html)
- [VPC Subnet Sizing Reference](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Subnets.html#vpc-sizing-ipv4)
- [Security Group Reference](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html)
- [NAT Gateway Documentation](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html)
- [AWS Networking Best Practices](https://docs.aws.amazon.com/whitepapers/latest/aws-vpc-connectivity-options/aws-vpc-connectivity-options.pdf)

---

Happy Clouding ☁️!

