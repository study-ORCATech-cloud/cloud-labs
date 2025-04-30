# LAB18: Deploying Containerized Applications with ECR and ECS

## ⚡ Lab Overview

In this advanced lab, you'll work with **Amazon Elastic Container Registry (ECR)** and **Amazon Elastic Container Service (ECS)**, AWS's core container services that enable you to build, store, and run containerized applications at scale. You'll learn how these services work together to provide a complete container management solution in the AWS cloud.

### 💼 Real-World DevOps Applications

Containers have become a fundamental technology in modern application development and deployment, offering several key advantages:

- **Consistency**: Eliminate "it works on my machine" problems with packaged dependencies
- **Portability**: Run the same container across development, testing, and production
- **Isolation**: Separate applications and their dependencies from the host system
- **Efficiency**: Share system resources while maintaining security boundaries
- **Scalability**: Scale containers independently and quickly
- **Immutability**: Deploy new versions without modifying existing containers

Common production use cases include:

- **Microservices Architecture**: Breaking monolithic applications into smaller, independent services
- **Batch Processing**: Running short-lived data processing tasks
- **CI/CD Pipelines**: Building, testing, and deploying applications reliably
- **DevOps Environments**: Maintaining consistent development and production environments
- **Hybrid Applications**: Running applications across on-premises and cloud infrastructure
- **Legacy Application Modernization**: Containerizing existing applications without rewriting them

---

## 🎯 Objectives

By the end of this lab, you will be able to:
- Create and manage private repositories in Amazon ECR
- Build, tag, and push Docker images to ECR
- Create an ECS cluster with the Fargate launch type
- Define task definitions for containerized applications
- Deploy and manage ECS services
- Configure application load balancing for container traffic
- Implement auto scaling for ECS services
- Monitor container performance and logs
- Update running containers with new versions
- Implement blue/green deployments for zero-downtime updates

---

## 🛠️ Prerequisites

- AWS account with administrative permissions
- Basic understanding of Docker and containers
- Docker installed on your local machine (for CLI implementation)
- Familiarity with networking concepts
- AWS CLI installed and configured (refer to [CLI Setup Guide](../../CLI-Setup.md))

---

# 🌐 PART 1: AWS MANAGEMENT CONSOLE IMPLEMENTATION

In this first part of the lab, you'll create and deploy containerized applications using ECR and ECS through the AWS Management Console.

## 📊 Step 1: Create a VPC for Container Networking

First, let's create a VPC for our container workloads:

1. Sign in to the AWS Management Console
2. Navigate to the **VPC** service
3. Click **Create VPC**
4. Choose **VPC and more**
5. Configure the settings:
   - **Name tag**: `lab18-container-vpc`
   - **IPv4 CIDR block**: `10.0.0.0/16`
   - **Number of Availability Zones**: `2`
   - **Number of public subnets**: `2`
   - **Number of private subnets**: `2`
   - **NAT gateways**: `1 per AZ` (for the lab, you can choose 1 to save costs)
   - **VPC endpoints**: `None` (we'll keep it simple for this lab)
6. Click **Create VPC**
7. Wait for the VPC creation to complete

## 🏗️ Step 2: Create an ECR Repository

Now, let's create a repository in Amazon ECR to store our container images:

1. Navigate to the **Amazon ECR** service
2. In the left navigation, click **Repositories**
3. Click **Create repository**
4. For **Repository name**, enter `lab18-webapp`
5. Under **Tag immutability**, select **Disable** (for simplicity)
6. Under **Scan on push**, select **Enable** to scan for vulnerabilities
7. Click **Create repository**

Once created, note the Repository URI (it will look like `<account-id>.dkr.ecr.<region>.amazonaws.com/lab18-webapp`)

## 📝 Step 3: Prepare a Simple Web Application Dockerfile

For this lab, we'll create a simple web application to deploy as a container:

1. On your local machine, create a new directory for our application:
   ```bash
   mkdir -p lab18-webapp && cd lab18-webapp
   ```

2. Create a file named `app.py` with the following content:
   ```python
   from flask import Flask
   import os
   import socket

   app = Flask(__name__)

   @app.route('/')
   def home():
       html = """
       <h1>Welcome to AWS ECS Lab!</h1>
       <p>Container ID: {hostname}</p>
       <p>This request was served by a containerized Flask application.</p>
       <p>Container version: v1.0</p>
       """
       return html.format(hostname=socket.gethostname())

   if __name__ == '__main__':
       app.run(host='0.0.0.0', port=80)
   ```

3. Create a file named `requirements.txt` with the following content:
   ```
   flask==2.0.1
   ```

4. Create a file named `Dockerfile` with the following content:
   ```Dockerfile
   FROM python:3.9-slim

   WORKDIR /app

   COPY requirements.txt .
   RUN pip install --no-cache-dir -r requirements.txt

   COPY app.py .

   EXPOSE 80

   CMD ["python", "app.py"]
   ```

## 🔄 Step 4: Build and Push the Docker Image to ECR

Now, let's build and push our Docker image to ECR:

1. Retrieve an authentication token and authenticate your Docker client to your registry:
   ```bash
   aws ecr get-login-password --region eu-west-1 | docker login --username AWS --password-stdin <account-id>.dkr.ecr.eu-west-1.amazonaws.com
   ```

2. Build your Docker image:
   ```bash
   docker build -t lab18-webapp:v1 .
   ```

3. Tag your image with the ECR repository URI:
   ```bash
   docker tag lab18-webapp:v1 <account-id>.dkr.ecr.eu-west-1.amazonaws.com/lab18-webapp:v1
   ```

4. Push the image to ECR:
   ```bash
   docker push <account-id>.dkr.ecr.eu-west-1.amazonaws.com/lab18-webapp:v1
   ```

5. Go back to the AWS Console, navigate to ECR, click on your repository, and verify that your image is now in the repository.

## 🌐 Step 5: Create an Application Load Balancer

Before we create our ECS cluster and services, let's set up an Application Load Balancer:

1. Navigate to the **EC2** service
2. In the left navigation under **Load Balancing**, click **Load Balancers**
3. Click **Create load balancer**
4. Select **Application Load Balancer** and click **Create**
5. For **Load balancer name**, enter `lab18-alb`
6. For **Scheme**, select **internet-facing**
7. For **IP address type**, select **ipv4**
8. For **VPC**, select the `lab18-container-vpc` we created earlier
9. For **Mappings**, select the two public subnets
10. Under **Security groups**, create a new security group:
    - **Name**: `lab18-alb-sg`
    - **Description**: `Security group for container ALB`
    - **VPC**: Select `lab18-container-vpc`
    - **Inbound rules**: Add a rule with Type: HTTP, Source: Anywhere
11. Click **Create security group**
12. Back in the load balancer creation, select the newly created security group
13. Under **Listeners and routing**, keep the default HTTP:80 and click **Create target group**:
    - **Target type**: Choose **IP addresses**
    - **Target group name**: `lab18-webapp-tg`
    - **Protocol**: `HTTP`
    - **Port**: `80`
    - **VPC**: Select `lab18-container-vpc`
    - **Protocol version**: `HTTP1`
    - **Health checks**: Keep defaults
    - Click **Next**
    - Skip adding targets and click **Create target group**
14. Back in the load balancer creation, refresh the target group list and select `lab18-webapp-tg`
15. Click **Create load balancer**
16. Wait for the load balancer to be provisioned (this may take a few minutes)

## 🚢 Step 6: Create an ECS Cluster

Now, let's create an ECS cluster to run our containers:

1. Navigate to the **Amazon ECS** service
2. Click **Clusters** in the left navigation
3. Click **Create Cluster**
4. For **Cluster name**, enter `lab18-cluster`
5. Under **Infrastructure**, select **AWS Fargate (serverless)**
6. Leave other settings as default and click **Create**

## 📋 Step 7: Create a Task Definition

Now, let's define the task that will run our container:

1. In the ECS console, click **Task Definitions** in the left navigation
2. Click **Create new Task Definition**
3. Choose **Fargate** and click **Next**
4. For **Task Definition Name**, enter `lab18-webapp-task`
5. For **Task Role**, select **Create new role** (this will create a role with minimal permissions)
6. For **Task execution role**, select **Create new role** (this allows ECS to pull the container image and publish logs)
7. For **Task size**, select:
   - **CPU**: `0.5 vCPU`
   - **Memory**: `1 GB`
8. For **Container Definitions**, click **Add container**:
   - **Container name**: `webapp`
   - **Image**: Enter your ECR image URI (`<account-id>.dkr.ecr.eu-west-1.amazonaws.com/lab18-webapp:v1`)
   - **Port mappings**: Add port `80` (Container port)
   - **Environment**: Leave as default
   - **Health check**: Leave as default
   - Under **Log collection**, enable **Auto-configure CloudWatch Logs**
   - Click **Add**
9. Click **Create**

## 🚀 Step 8: Create an ECS Service

Now, let's deploy our container as a service:

1. In the ECS console, click **Clusters** in the left navigation
2. Click on the `lab18-cluster` we created earlier
3. In the **Services** tab, click **Create**
4. For **Launch type**, select **FARGATE**
5. For **Task Definition**, select the `lab18-webapp-task` we created
6. For **Service name**, enter `lab18-webapp-service`
7. For **Service type**, select **REPLICA**
8. For **Number of tasks**, enter `2`
9. For **Deployment type**, select **Rolling update**
10. Click **Next**
11. For **Networking**:
    - **VPC**: Select `lab18-container-vpc`
    - **Subnets**: Select the two private subnets
    - **Security groups**: Click **Create new security group**
        - **Security group name**: `lab18-container-sg`
        - **Description**: `Security group for containers`
        - **Rules**: Add an inbound rule for HTTP on port 80 from the ALB security group
    - **Public IP**: Disable (since we're using an ALB)
12. For **Load balancing**:
    - **Load balancer type**: Select **Application Load Balancer**
    - **Load balancer name**: Select `lab18-alb`
    - **Container to load balance**: Select the container and port mapping (webapp:80)
    - **Target group name**: Select `lab18-webapp-tg`
13. Click **Next**
14. For **Auto Scaling**:
    - **Service Auto Scaling**: Select **Use Service Auto Scaling to adjust your service's desired count**
    - **Minimum number of tasks**: `2`
    - **Desired number of tasks**: `2`
    - **Maximum number of tasks**: `6`
    - **IAM role for Auto Scaling**: Select **Create new role**
    - **Scaling policy type**: Select **Target tracking**
    - **Policy name**: `lab18-cpu-tracking`
    - **ECS service metric**: Select **ECSServiceAverageCPUUtilization**
    - **Target value**: `70`
    - **Scale-out cooldown period**: `60`
    - **Scale-in cooldown period**: `60`
15. Click **Next**
16. Review your settings and click **Create Service**
17. Click **View Service**

## 🔍 Step 9: Test the Application

Once your service is running, let's test the application:

1. In the ECS console, click on the `lab18-webapp-service` we just created
2. Go to the **Networking** tab
3. Find the **Load Balancer** and click on the DNS name of the load balancer (or copy it)
4. Paste the DNS name into your browser to access your web application
5. You should see your application running with the container ID displayed

## 📊 Step 10: Update the Application

Now, let's update our application with a new version:

1. On your local machine, modify the `app.py` file to change the version:
   ```python
   # Change this line:
   <p>Container version: v1.0</p>
   # To:
   <p>Container version: v2.0</p>
   ```

2. Build a new version of the Docker image:
   ```bash
   docker build -t lab18-webapp:v2 .
   ```

3. Tag the new image:
   ```bash
   docker tag lab18-webapp:v2 <account-id>.dkr.ecr.eu-west-1.amazonaws.com/lab18-webapp:v2
   ```

4. Push the new image to ECR:
   ```bash
   docker push <account-id>.dkr.ecr.eu-west-1.amazonaws.com/lab18-webapp:v2
   ```

5. Go back to the AWS Console, navigate to the ECS service
6. Click on the `lab18-webapp-task` task definition
7. Click **Create new revision**
8. Update the container image to use the new `:v2` tag
9. Click **Create**

10. Navigate back to the `lab18-cluster`
11. Select the `lab18-webapp-service`
12. Click **Update**
13. Update the task definition to use the latest revision
14. Keep all other settings the same
15. Click **Update Service**

16. Refresh the service page and watch the **Deployments** tab to see the update progress
17. Once the new deployment is complete, refresh your browser to see the updated application with the new version

## 🧹 Step 11: Console Implementation Cleanup

To avoid ongoing charges, let's clean up the resources created:

1. Navigate to the **ECS** service
2. Select the `lab18-cluster`
3. Select the `lab18-webapp-service`
4. Click **Delete**
5. Confirm the deletion by typing `delete` and click **Delete**
6. Wait for the service to be deleted

7. Navigate to **Task Definitions**
8. Select the `lab18-webapp-task`
9. Select all revisions and click **Deregister**
10. Confirm by clicking **Deregister**

11. Navigate back to **Clusters**
12. Select the `lab18-cluster`
13. Click **Delete Cluster**
14. Confirm by clicking **Delete**

15. Navigate to the **EC2** service
16. In the left navigation, click **Load Balancers**
17. Select the `lab18-alb` we created
18. Click **Actions** > **Delete**
19. Confirm by clicking **Yes, Delete**

20. In the left navigation, click **Target Groups**
21. Select the `lab18-webapp-tg` target group
22. Click **Actions** > **Delete**
23. Confirm the deletion

24. In the left navigation, click **Security Groups**
25. Delete the `lab18-alb-sg` and `lab18-container-sg` security groups (ensure the ALB is deleted first)

26. Navigate to the **ECR** service
27. Select the `lab18-webapp` repository
28. Click **Delete**
29. Type `delete` to confirm and click **Delete**

30. Navigate to the **VPC** service
31. Select the `lab18-container-vpc` we created
32. Click **Actions** > **Delete VPC**
33. Select all the associated resources
34. Click **Delete**

35. Navigate to **CloudWatch** service
36. In the left navigation, click **Log groups**
37. Find and delete any log groups related to our ECS tasks

Note: It might take a few minutes for all resources to be deleted. Make sure to check that all resources have been properly cleaned up to avoid any unexpected charges.

---

# 🖥️ PART 2: AWS CLI IMPLEMENTATION

In this second part of the lab, you'll perform similar operations using the AWS Command Line Interface (CLI).

## 📊 Step 1: Create a VPC for Container Networking (CLI)

First, let's create a VPC for our containers using the CLI:

```bash
# Create a VPC
VPC_ID=$(aws ec2 create-vpc \
  --cidr-block 10.0.0.0/16 \
  --tag-specifications 'ResourceType=vpc,Tags=[{Key=Name,Value=lab18-cli-vpc}]' \
  --query 'Vpc.VpcId' \
  --output text)

echo "Created VPC: $VPC_ID"
echo "VPC_ID=$VPC_ID" > lab18-vars.sh

# Enable DNS hostnames
aws ec2 modify-vpc-attribute \
  --vpc-id $VPC_ID \
  --enable-dns-hostnames

# Create an Internet Gateway
IGW_ID=$(aws ec2 create-internet-gateway \
  --tag-specifications 'ResourceType=internet-gateway,Tags=[{Key=Name,Value=lab18-cli-igw}]' \
  --query 'InternetGateway.InternetGatewayId' \
  --output text)

echo "Created Internet Gateway: $IGW_ID"
echo "IGW_ID=$IGW_ID" >> lab18-vars.sh

# Attach the Internet Gateway to the VPC
aws ec2 attach-internet-gateway \
  --vpc-id $VPC_ID \
  --internet-gateway-id $IGW_ID

# Get available Availability Zones
AZ1=$(aws ec2 describe-availability-zones --query 'AvailabilityZones[0].ZoneName' --output text)
AZ2=$(aws ec2 describe-availability-zones --query 'AvailabilityZones[1].ZoneName' --output text)

echo "Using Availability Zones: $AZ1 and $AZ2"
echo "AZ1=$AZ1" >> lab18-vars.sh
echo "AZ2=$AZ2" >> lab18-vars.sh

# Create public subnets
PUBLIC_SUBNET_1=$(aws ec2 create-subnet \
  --vpc-id $VPC_ID \
  --cidr-block 10.0.1.0/24 \
  --availability-zone $AZ1 \
  --tag-specifications 'ResourceType=subnet,Tags=[{Key=Name,Value=lab18-cli-public-1}]' \
  --query 'Subnet.SubnetId' \
  --output text)

PUBLIC_SUBNET_2=$(aws ec2 create-subnet \
  --vpc-id $VPC_ID \
  --cidr-block 10.0.2.0/24 \
  --availability-zone $AZ2 \
  --tag-specifications 'ResourceType=subnet,Tags=[{Key=Name,Value=lab18-cli-public-2}]' \
  --query 'Subnet.SubnetId' \
  --output text)

echo "Created Public Subnets: $PUBLIC_SUBNET_1 and $PUBLIC_SUBNET_2"
echo "PUBLIC_SUBNET_1=$PUBLIC_SUBNET_1" >> lab18-vars.sh
echo "PUBLIC_SUBNET_2=$PUBLIC_SUBNET_2" >> lab18-vars.sh

# Enable auto-assign public IP on public subnets
aws ec2 modify-subnet-attribute \
  --subnet-id $PUBLIC_SUBNET_1 \
  --map-public-ip-on-launch

aws ec2 modify-subnet-attribute \
  --subnet-id $PUBLIC_SUBNET_2 \
  --map-public-ip-on-launch

# Create private subnets
PRIVATE_SUBNET_1=$(aws ec2 create-subnet \
  --vpc-id $VPC_ID \
  --cidr-block 10.0.3.0/24 \
  --availability-zone $AZ1 \
  --tag-specifications 'ResourceType=subnet,Tags=[{Key=Name,Value=lab18-cli-private-1}]' \
  --query 'Subnet.SubnetId' \
  --output text)

PRIVATE_SUBNET_2=$(aws ec2 create-subnet \
  --vpc-id $VPC_ID \
  --cidr-block 10.0.4.0/24 \
  --availability-zone $AZ2 \
  --tag-specifications 'ResourceType=subnet,Tags=[{Key=Name,Value=lab18-cli-private-2}]' \
  --query 'Subnet.SubnetId' \
  --output text)

echo "Created Private Subnets: $PRIVATE_SUBNET_1 and $PRIVATE_SUBNET_2"
echo "PRIVATE_SUBNET_1=$PRIVATE_SUBNET_1" >> lab18-vars.sh
echo "PRIVATE_SUBNET_2=$PRIVATE_SUBNET_2" >> lab18-vars.sh

# Create a route table for public subnets
PUBLIC_RTB=$(aws ec2 create-route-table \
  --vpc-id $VPC_ID \
  --tag-specifications 'ResourceType=route-table,Tags=[{Key=Name,Value=lab18-cli-public-rtb}]' \
  --query 'RouteTable.RouteTableId' \
  --output text)

echo "Created Public Route Table: $PUBLIC_RTB"
echo "PUBLIC_RTB=$PUBLIC_RTB" >> lab18-vars.sh

# Create route to Internet Gateway
aws ec2 create-route \
  --route-table-id $PUBLIC_RTB \
  --destination-cidr-block 0.0.0.0/0 \
  --gateway-id $IGW_ID

# Associate public subnets with the public route table
aws ec2 associate-route-table \
  --subnet-id $PUBLIC_SUBNET_1 \
  --route-table-id $PUBLIC_RTB

aws ec2 associate-route-table \
  --subnet-id $PUBLIC_SUBNET_2 \
  --route-table-id $PUBLIC_RTB

# Create a NAT Gateway
ELASTIC_IP=$(aws ec2 allocate-address \
  --domain vpc \
  --query 'AllocationId' \
  --output text)

echo "Created Elastic IP: $ELASTIC_IP"
echo "ELASTIC_IP=$ELASTIC_IP" >> lab18-vars.sh

NAT_GATEWAY=$(aws ec2 create-nat-gateway \
  --subnet-id $PUBLIC_SUBNET_1 \
  --allocation-id $ELASTIC_IP \
  --tag-specifications 'ResourceType=natgateway,Tags=[{Key=Name,Value=lab18-cli-natgw}]' \
  --query 'NatGateway.NatGatewayId' \
  --output text)

echo "Creating NAT Gateway: $NAT_GATEWAY (this may take a few minutes)"
echo "NAT_GATEWAY=$NAT_GATEWAY" >> lab18-vars.sh

# Wait for NAT Gateway to become available
echo "Waiting for NAT Gateway to become available..."
aws ec2 wait nat-gateway-available --nat-gateway-ids $NAT_GATEWAY

# Create a route table for private subnets
PRIVATE_RTB=$(aws ec2 create-route-table \
  --vpc-id $VPC_ID \
  --tag-specifications 'ResourceType=route-table,Tags=[{Key=Name,Value=lab18-cli-private-rtb}]' \
  --query 'RouteTable.RouteTableId' \
  --output text)

echo "Created Private Route Table: $PRIVATE_RTB"
echo "PRIVATE_RTB=$PRIVATE_RTB" >> lab18-vars.sh

# Create route to NAT Gateway
aws ec2 create-route \
  --route-table-id $PRIVATE_RTB \
  --destination-cidr-block 0.0.0.0/0 \
  --nat-gateway-id $NAT_GATEWAY

# Associate private subnets with the private route table
aws ec2 associate-route-table \
  --subnet-id $PRIVATE_SUBNET_1 \
  --route-table-id $PRIVATE_RTB

aws ec2 associate-route-table \
  --subnet-id $PRIVATE_SUBNET_2 \
  --route-table-id $PRIVATE_RTB

echo "VPC setup complete!"
```

## 🏗️ Step 2: Create an ECR Repository (CLI)

Now, let's create an ECR repository to store our container images:

```bash
# Source the variables file
source lab18-vars.sh

# Create ECR repository
REPO_URI=$(aws ecr create-repository \
  --repository-name lab18-cli-webapp \
  --image-scanning-configuration scanOnPush=true \
  --query 'repository.repositoryUri' \
  --output text)

# Extract account ID and region from the repository URI
ACCOUNT_ID=$(echo $REPO_URI | cut -d'.' -f1)
REGION=$(echo $REPO_URI | cut -d'.' -f4)

echo "Created ECR Repository: $REPO_URI"
echo "REPO_URI=$REPO_URI" >> lab18-vars.sh
echo "ACCOUNT_ID=$ACCOUNT_ID" >> lab18-vars.sh
echo "REGION=$REGION" >> lab18-vars.sh
```

## 📝 Step 3: Prepare a Simple Web Application Dockerfile (CLI)

Let's create the same web application as in the console part:

```bash
# Create directory for the application
mkdir -p lab18-cli-webapp && cd lab18-cli-webapp

# Create app.py file
cat > app.py << 'EOL'
from flask import Flask
import os
import socket

app = Flask(__name__)

@app.route('/')
def home():
    html = """
    <h1>Welcome to AWS ECS Lab (CLI)!</h1>
    <p>Container ID: {hostname}</p>
    <p>This request was served by a containerized Flask application.</p>
    <p>Container version: v1.0</p>
    """
    return html.format(hostname=socket.gethostname())

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=80)
EOL

# Create requirements.txt file
cat > requirements.txt << 'EOL'
flask==2.0.1
EOL

# Create Dockerfile
cat > Dockerfile << 'EOL'
FROM python:3.9-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY app.py .

EXPOSE 80

CMD ["python", "app.py"]
EOL

echo "Application files created"
```

## 🔄 Step 4: Build and Push the Docker Image to ECR (CLI)

Now, let's build and push our Docker image to ECR:

```bash
# Source the variables file
source ../lab18-vars.sh

# Authenticate to ECR
aws ecr get-login-password --region $REGION | docker login --username AWS --password-stdin $REPO_URI

# Build the Docker image
docker build -t lab18-cli-webapp:v1 .

# Tag the image with the ECR repository URI
docker tag lab18-cli-webapp:v1 $REPO_URI:v1

# Push the image to ECR
docker push $REPO_URI:v1

echo "Image pushed to ECR: $REPO_URI:v1"
cd ..
```

## 🌐 Step 5: Create an Application Load Balancer (CLI)

Let's set up an Application Load Balancer for our containers:

```bash
# Source the variables file
source lab18-vars.sh

# Create a security group for the ALB
ALB_SG=$(aws ec2 create-security-group \
  --group-name lab18-cli-alb-sg \
  --description "Security group for container ALB" \
  --vpc-id $VPC_ID \
  --query 'GroupId' \
  --output text)

echo "Created ALB Security Group: $ALB_SG"
echo "ALB_SG=$ALB_SG" >> lab18-vars.sh

# Add inbound rule to allow HTTP traffic from anywhere
aws ec2 authorize-security-group-ingress \
  --group-id $ALB_SG \
  --protocol tcp \
  --port 80 \
  --cidr 0.0.0.0/0

# Create a security group for ECS tasks
ECS_SG=$(aws ec2 create-security-group \
  --group-name lab18-cli-ecs-sg \
  --description "Security group for ECS containers" \
  --vpc-id $VPC_ID \
  --query 'GroupId' \
  --output text)

echo "Created ECS Security Group: $ECS_SG"
echo "ECS_SG=$ECS_SG" >> lab18-vars.sh

# Add inbound rule to allow traffic from the ALB security group
aws ec2 authorize-security-group-ingress \
  --group-id $ECS_SG \
  --protocol tcp \
  --port 80 \
  --source-group $ALB_SG

# Create a target group
TG_ARN=$(aws elbv2 create-target-group \
  --name lab18-cli-tg \
  --protocol HTTP \
  --port 80 \
  --vpc-id $VPC_ID \
  --target-type ip \
  --health-check-path / \
  --health-check-interval-seconds 30 \
  --health-check-timeout-seconds 5 \
  --healthy-threshold-count 2 \
  --unhealthy-threshold-count 2 \
  --query 'TargetGroups[0].TargetGroupArn' \
  --output text)

echo "Created Target Group: $TG_ARN"
echo "TG_ARN=$TG_ARN" >> lab18-vars.sh

# Create an Application Load Balancer
ALB_ARN=$(aws elbv2 create-load-balancer \
  --name lab18-cli-alb \
  --subnets $PUBLIC_SUBNET_1 $PUBLIC_SUBNET_2 \
  --security-groups $ALB_SG \
  --query 'LoadBalancers[0].LoadBalancerArn' \
  --output text)

echo "Created Application Load Balancer: $ALB_ARN"
echo "ALB_ARN=$ALB_ARN" >> lab18-vars.sh

# Wait for the ALB to be active
echo "Waiting for ALB to become active..."
aws elbv2 wait load-balancer-available --load-balancer-arns $ALB_ARN

# Get the ALB DNS name
ALB_DNS=$(aws elbv2 describe-load-balancers \
  --load-balancer-arns $ALB_ARN \
  --query 'LoadBalancers[0].DNSName' \
  --output text)

echo "ALB DNS: $ALB_DNS"
echo "ALB_DNS=$ALB_DNS" >> lab18-vars.sh

# Create a listener
LISTENER_ARN=$(aws elbv2 create-listener \
  --load-balancer-arn $ALB_ARN \
  --protocol HTTP \
  --port 80 \
  --default-actions Type=forward,TargetGroupArn=$TG_ARN \
  --query 'Listeners[0].ListenerArn' \
  --output text)

echo "Created Listener: $LISTENER_ARN"
echo "LISTENER_ARN=$LISTENER_ARN" >> lab18-vars.sh
```

## 🚢 Step 6: Create an ECS Cluster (CLI)

Now, let's create an ECS cluster:

```bash
# Source the variables file
source lab18-vars.sh

# Create an ECS cluster
CLUSTER_ARN=$(aws ecs create-cluster \
  --cluster-name lab18-cli-cluster \
  --capacity-providers FARGATE FARGATE_SPOT \
  --default-capacity-provider-strategy capacityProvider=FARGATE,weight=1 \
  --query 'cluster.clusterArn' \
  --output text)

echo "Created ECS Cluster: $CLUSTER_ARN"
echo "CLUSTER_ARN=$CLUSTER_ARN" >> lab18-vars.sh
```

## 📋 Step 7: Create a Task Definition (CLI)

Let's define the task that will run our container:

```bash
# Source the variables file
source lab18-vars.sh

# Create an ECS task execution role
TASK_EXECUTION_ROLE=$(aws iam create-role \
  --role-name lab18-cli-task-execution-role \
  --assume-role-policy-document '{
    "Version": "2012-10-17",
    "Statement": [
      {
        "Effect": "Allow",
        "Principal": {
          "Service": "ecs-tasks.amazonaws.com"
        },
        "Action": "sts:AssumeRole"
      }
    ]
  }' \
  --query 'Role.Arn' \
  --output text)

echo "Created Task Execution Role: $TASK_EXECUTION_ROLE"
echo "TASK_EXECUTION_ROLE=$TASK_EXECUTION_ROLE" >> lab18-vars.sh

# Attach the task execution policy
aws iam attach-role-policy \
  --role-name lab18-cli-task-execution-role \
  --policy-arn arn:aws:iam::aws:policy/service-role/AmazonECSTaskExecutionRolePolicy

# Create task definition JSON file
cat > task-definition.json << EOL
{
  "family": "lab18-cli-webapp-task",
  "networkMode": "awsvpc",
  "requiresCompatibilities": ["FARGATE"],
  "cpu": "512",
  "memory": "1024",
  "executionRoleArn": "${TASK_EXECUTION_ROLE}",
  "containerDefinitions": [
    {
      "name": "webapp",
      "image": "${REPO_URI}:v1",
      "essential": true,
      "portMappings": [
        {
          "containerPort": 80,
          "hostPort": 80,
          "protocol": "tcp"
        }
      ],
      "logConfiguration": {
        "logDriver": "awslogs",
        "options": {
          "awslogs-group": "/ecs/lab18-cli-webapp-task",
          "awslogs-region": "${REGION}",
          "awslogs-stream-prefix": "ecs",
          "awslogs-create-group": "true"
        }
      }
    }
  ]
}
EOL

# Create the CloudWatch Logs group
aws logs create-log-group \
  --log-group-name /ecs/lab18-cli-webapp-task

# Register the task definition
TASK_DEFINITION_ARN=$(aws ecs register-task-definition \
  --cli-input-json file://task-definition.json \
  --query 'taskDefinition.taskDefinitionArn' \
  --output text)

echo "Registered Task Definition: $TASK_DEFINITION_ARN"
echo "TASK_DEFINITION_ARN=$TASK_DEFINITION_ARN" >> lab18-vars.sh
```

## 🚀 Step 8: Create an ECS Service (CLI)

Now, let's deploy our container as a service:

```bash
# Source the variables file
source lab18-vars.sh

# Create auto-scaling IAM role
AUTO_SCALING_ROLE=$(aws iam create-role \
  --role-name lab18-cli-ecs-autoscale-role \
  --assume-role-policy-document '{
    "Version": "2012-10-17",
    "Statement": [
      {
        "Effect": "Allow",
        "Principal": {
          "Service": "application-autoscaling.amazonaws.com"
        },
        "Action": "sts:AssumeRole"
      }
    ]
  }' \
  --query 'Role.Arn' \
  --output text)

echo "Created Auto Scaling Role: $AUTO_SCALING_ROLE"
echo "AUTO_SCALING_ROLE=$AUTO_SCALING_ROLE" >> lab18-vars.sh

# Attach the auto-scaling policy
aws iam attach-role-policy \
  --role-name lab18-cli-ecs-autoscale-role \
  --policy-arn arn:aws:iam::aws:policy/service-role/AmazonEC2ContainerServiceAutoscaleRole

# Create the ECS service
SERVICE_ARN=$(aws ecs create-service \
  --cluster lab18-cli-cluster \
  --service-name lab18-cli-webapp-service \
  --task-definition lab18-cli-webapp-task \
  --desired-count 2 \
  --launch-type FARGATE \
  --platform-version LATEST \
  --network-configuration "awsvpcConfiguration={subnets=[$PRIVATE_SUBNET_1,$PRIVATE_SUBNET_2],securityGroups=[$ECS_SG],assignPublicIp=DISABLED}" \
  --load-balancers "targetGroupArn=$TG_ARN,containerName=webapp,containerPort=80" \
  --scheduling-strategy REPLICA \
  --deployment-configuration "deploymentCircuitBreaker={enable=true,rollback=true},maximumPercent=200,minimumHealthyPercent=100" \
  --deployment-controller "type=ECS" \
  --health-check-grace-period-seconds 60 \
  --query 'service.serviceArn' \
  --output text)

echo "Created ECS Service: $SERVICE_ARN"
echo "SERVICE_ARN=$SERVICE_ARN" >> lab18-vars.sh

# Wait for the service to be stable
echo "Waiting for the service to become stable (this may take a few minutes)..."
aws ecs wait services-stable \
  --cluster lab18-cli-cluster \
  --services lab18-cli-webapp-service
```

## 📈 Step 9: Configure Auto Scaling for the Service (CLI)

Let's set up auto scaling for our ECS service:

```bash
# Source the variables file
source lab18-vars.sh

# Register a scalable target
aws application-autoscaling register-scalable-target \
  --service-namespace ecs \
  --resource-id service/lab18-cli-cluster/lab18-cli-webapp-service \
  --scalable-dimension ecs:service:DesiredCount \
  --min-capacity 2 \
  --max-capacity 6 \
  --role-arn $AUTO_SCALING_ROLE

# Create a scaling policy
aws application-autoscaling put-scaling-policy \
  --service-namespace ecs \
  --resource-id service/lab18-cli-cluster/lab18-cli-webapp-service \
  --scalable-dimension ecs:service:DesiredCount \
  --policy-name lab18-cli-cpu-tracking \
  --policy-type TargetTrackingScaling \
  --target-tracking-scaling-policy-configuration '{
    "TargetValue": 70.0,
    "PredefinedMetricSpecification": {
      "PredefinedMetricType": "ECSServiceAverageCPUUtilization"
    },
    "ScaleOutCooldown": 60,
    "ScaleInCooldown": 60
  }'

echo "Auto Scaling configured for ECS service"
```

## 🔍 Step 10: Test the Application (CLI)

Now, let's test our application:

```bash
# Source the variables file
source lab18-vars.sh

echo "Application is available at: http://$ALB_DNS"
echo "Try accessing this URL in your web browser"
```

## 📊 Step 11: Update the Application (CLI)

Let's update our application with a new version:

```bash
# Navigate to the application directory
cd lab18-cli-webapp

# Update the app.py file
cat > app.py << 'EOL'
from flask import Flask
import os
import socket

app = Flask(__name__)

@app.route('/')
def home():
    html = """
    <h1>Welcome to AWS ECS Lab (CLI)!</h1>
    <p>Container ID: {hostname}</p>
    <p>This request was served by a containerized Flask application.</p>
    <p>Container version: v2.0</p>
    """
    return html.format(hostname=socket.gethostname())

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=80)
EOL

# Source the variables file
source ../lab18-vars.sh

# Build a new version of the Docker image
docker build -t lab18-cli-webapp:v2 .

# Tag the new image
docker tag lab18-cli-webapp:v2 $REPO_URI:v2

# Push the new image to ECR
docker push $REPO_URI:v2

echo "New image pushed to ECR: $REPO_URI:v2"
cd ..

# Update the task definition to use the new image
cat > task-definition-v2.json << EOL
{
  "family": "lab18-cli-webapp-task",
  "networkMode": "awsvpc",
  "requiresCompatibilities": ["FARGATE"],
  "cpu": "512",
  "memory": "1024",
  "executionRoleArn": "${TASK_EXECUTION_ROLE}",
  "containerDefinitions": [
    {
      "name": "webapp",
      "image": "${REPO_URI}:v2",
      "essential": true,
      "portMappings": [
        {
          "containerPort": 80,
          "hostPort": 80,
          "protocol": "tcp"
        }
      ],
      "logConfiguration": {
        "logDriver": "awslogs",
        "options": {
          "awslogs-group": "/ecs/lab18-cli-webapp-task",
          "awslogs-region": "${REGION}",
          "awslogs-stream-prefix": "ecs",
          "awslogs-create-group": "true"
        }
      }
    }
  ]
}
EOL

# Register the new task definition
TASK_DEFINITION_ARN_V2=$(aws ecs register-task-definition \
  --cli-input-json file://task-definition-v2.json \
  --query 'taskDefinition.taskDefinitionArn' \
  --output text)

echo "Registered new Task Definition: $TASK_DEFINITION_ARN_V2"

# Update the service with the new task definition
aws ecs update-service \
  --cluster lab18-cli-cluster \
  --service lab18-cli-webapp-service \
  --task-definition $TASK_DEFINITION_ARN_V2 \
  --force-new-deployment

echo "Service update initiated, waiting for stability..."

# Wait for the service to be stable
aws ecs wait services-stable \
  --cluster lab18-cli-cluster \
  --services lab18-cli-webapp-service

echo "Service updated successfully! Refresh your browser to see the updated application."
```

## 🧹 Step 12: CLI Implementation Cleanup

To avoid ongoing charges, let's clean up all the resources we created:

```bash
# Source the variables file
source lab18-vars.sh

# Delete the ECS service
echo "Deleting ECS service..."
aws ecs update-service \
  --cluster lab18-cli-cluster \
  --service lab18-cli-webapp-service \
  --desired-count 0

aws ecs delete-service \
  --cluster lab18-cli-cluster \
  --service lab18-cli-webapp-service \
  --force

# Wait for the service to be deleted
echo "Waiting for service to be deleted..."
sleep 30

# Delete the ECS cluster
echo "Deleting ECS cluster..."
aws ecs delete-cluster \
  --cluster lab18-cli-cluster

# Deregister task definitions
echo "Deregistering task definitions..."
aws ecs list-task-definitions \
  --family-prefix lab18-cli-webapp-task \
  --query 'taskDefinitionArns' \
  --output text | tr '\t' '\n' | while read arn; do
    aws ecs deregister-task-definition --task-definition $arn
  done

# Delete the CloudWatch Logs group
echo "Deleting CloudWatch Logs group..."
aws logs delete-log-group \
  --log-group-name /ecs/lab18-cli-webapp-task

# Delete the Application Load Balancer
echo "Deleting Load Balancer..."
aws elbv2 delete-load-balancer \
  --load-balancer-arn $ALB_ARN

# Wait for the load balancer to be deleted
echo "Waiting for load balancer to be deleted..."
sleep 30

# Delete the target group
echo "Deleting Target Group..."
aws elbv2 delete-target-group \
  --target-group-arn $TG_ARN

# Delete security groups
echo "Deleting Security Groups..."
aws ec2 delete-security-group \
  --group-id $ECS_SG

aws ec2 delete-security-group \
  --group-id $ALB_SG

# Delete the NAT Gateway
echo "Deleting NAT Gateway..."
aws ec2 delete-nat-gateway \
  --nat-gateway-id $NAT_GATEWAY

# Wait for NAT Gateway to be deleted
echo "Waiting for NAT Gateway to be deleted..."
aws ec2 wait nat-gateway-deleted \
  --nat-gateway-ids $NAT_GATEWAY

# Release the Elastic IP
echo "Releasing Elastic IP..."
aws ec2 release-address \
  --allocation-id $ELASTIC_IP

# Delete route tables
echo "Deleting Route Tables..."
aws ec2 disassociate-route-table \
  --association-id $(aws ec2 describe-route-tables \
    --route-table-id $PUBLIC_RTB \
    --query 'RouteTables[0].Associations[0].RouteTableAssociationId' \
    --output text)

aws ec2 disassociate-route-table \
  --association-id $(aws ec2 describe-route-tables \
    --route-table-id $PUBLIC_RTB \
    --query 'RouteTables[0].Associations[1].RouteTableAssociationId' \
    --output text)

aws ec2 disassociate-route-table \
  --association-id $(aws ec2 describe-route-tables \
    --route-table-id $PRIVATE_RTB \
    --query 'RouteTables[0].Associations[0].RouteTableAssociationId' \
    --output text)

aws ec2 disassociate-route-table \
  --association-id $(aws ec2 describe-route-tables \
    --route-table-id $PRIVATE_RTB \
    --query 'RouteTables[0].Associations[1].RouteTableAssociationId' \
    --output text)

aws ec2 delete-route-table \
  --route-table-id $PUBLIC_RTB

aws ec2 delete-route-table \
  --route-table-id $PRIVATE_RTB

# Detach the Internet Gateway
echo "Detaching Internet Gateway..."
aws ec2 detach-internet-gateway \
  --internet-gateway-id $IGW_ID \
  --vpc-id $VPC_ID

# Delete the Internet Gateway
echo "Deleting Internet Gateway..."
aws ec2 delete-internet-gateway \
  --internet-gateway-id $IGW_ID

# Delete the subnets
echo "Deleting Subnets..."
aws ec2 delete-subnet \
  --subnet-id $PUBLIC_SUBNET_1

aws ec2 delete-subnet \
  --subnet-id $PUBLIC_SUBNET_2

aws ec2 delete-subnet \
  --subnet-id $PRIVATE_SUBNET_1

aws ec2 delete-subnet \
  --subnet-id $PRIVATE_SUBNET_2

# Delete the VPC
echo "Deleting VPC..."
aws ec2 delete-vpc \
  --vpc-id $VPC_ID

# Delete the ECR repository
echo "Deleting ECR Repository..."
aws ecr delete-repository \
  --repository-name lab18-cli-webapp \
  --force

# Delete IAM roles
echo "Detaching and deleting IAM Roles..."
aws iam detach-role-policy \
  --role-name lab18-cli-task-execution-role \
  --policy-arn arn:aws:iam::aws:policy/service-role/AmazonECSTaskExecutionRolePolicy

aws iam detach-role-policy \
  --role-name lab18-cli-ecs-autoscale-role \
  --policy-arn arn:aws:iam::aws:policy/service-role/AmazonEC2ContainerServiceAutoscaleRole

aws iam delete-role \
  --role-name lab18-cli-task-execution-role

aws iam delete-role \
  --role-name lab18-cli-ecs-autoscale-role

# Clean up local files
echo "Cleaning up local files..."
rm -rf lab18-cli-webapp
rm -f task-definition.json task-definition-v2.json lab18-vars.sh

echo "Cleanup complete!"
```

---

## 🧪 Optional Challenges

To deepen your understanding of ECS and ECR, try these additional challenges:

1. **Multi-Container Task**: Create a task definition with multiple containers (e.g., an app container and a sidecar)
2. **ECS Service Blue/Green Deployment**: Implement a blue/green deployment using AWS CodeDeploy
3. **Private ECR Cross-Account Access**: Configure cross-account access to your ECR repository
4. **Custom Container Monitoring**: Set up detailed container monitoring using CloudWatch Container Insights
5. **Docker Compose to ECS**: Convert a Docker Compose file to ECS task definition using the ECS CLI
6. **ECS Service Discovery**: Implement service discovery for inter-service communication
7. **ECS Scheduled Tasks**: Create scheduled tasks for batch processing

---

## 🎓 Summary

In this lab, you've learned how to:
- Create and manage ECR repositories for storing container images
- Build and push Docker images to ECR
- Set up networking infrastructure for container workloads
- Deploy containerized applications using ECS with Fargate
- Configure Application Load Balancers for container traffic
- Set up auto scaling for ECS services
- Update running containers with new versions
- Clean up container resources properly

These skills are essential for implementing modern containerized applications in AWS, allowing you to deploy scalable and reliable services while maintaining consistent environments across your development and production workloads. 