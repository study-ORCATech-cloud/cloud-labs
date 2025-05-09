# LAB05: Deploy Load Balancer with Auto Scaling (AWS Console & CLI)

## ⚖️ Lab Overview

In this lab, you'll build a **highly available, scalable application infrastructure** using an **Application Load Balancer (ALB)** and an **Auto Scaling Group (ASG)**. You'll deploy a launch template, configure scaling policies, and route traffic using the ALB. This lab introduces core concepts for building resilient, production-ready applications in AWS.

### 📚 Core Concepts for Beginners

- **Load Balancer**: Acts like a traffic cop, distributing incoming requests across multiple servers to prevent any single server from becoming overwhelmed.
- **Auto Scaling Group**: A fleet of servers that automatically grows or shrinks based on demand.
- **Launch Template**: A blueprint defining exactly how to create each server in your Auto Scaling Group.
- **High Availability**: Designing systems to remain operational even if some components fail.
- **Target Group**: A collection of servers that the load balancer routes traffic to.
- **Health Checks**: Regular tests to verify if servers are functioning properly.

### 🏗️ Real-World Analogy

Think of this architecture like a modern restaurant:
- The **Load Balancer** is the host/hostess at the entrance, directing customers to available tables
- The **Auto Scaling Group** is the dining area that can add or remove tables as needed
- The **Launch Template** is the restaurant's standardized table setup procedure
- **Health Checks** are like the manager walking around to make sure each table is properly serviced

## 🎯 Objectives

By the end of this lab, you will be able to:

- Create an EC2 launch template with a basic web server configuration
- Deploy an Auto Scaling Group that spans multiple Availability Zones for high availability
- Create an Application Load Balancer to distribute traffic
- Configure target groups and health checks
- Test load balancing and automatic scaling behavior
- Understand how these components work together to create resilient applications

## 🛠️ Prerequisites

- AWS account
- IAM permissions for EC2, Elastic Load Balancing, and Auto Scaling
- AWS CLI tools installed and configured (refer to the [CLI Setup Guide](../../CLI-Setup.md))
- Basic understanding of EC2 and networking concepts from previous labs

## 🌐 Architecture for This Lab

We'll build the following architecture:

```
                           Internet
                               │
                               ▼
                     Application Load Balancer
                               │
                               ▼
                        Target Group
                      /       │       \
                     ▼        ▼        ▼
             ┌─────────┐ ┌─────────┐ ┌─────────┐
             │Instance 1│ │Instance 2│ │Instance 3│  ← Auto Scaling Group
             │   AZ-1   │ │   AZ-2   │ │   AZ-1   │    (created from Launch Template)
             └─────────┘ └─────────┘ └─────────┘
```

## 🧱 Part 1: Create a Launch Template

A Launch Template defines the configuration for EC2 instances that will be created by your Auto Scaling Group. This includes the AMI, instance type, security groups, and user data.

### Step 1: Create a Security Group for Web Servers

1. Navigate to **EC2 > Security Groups** in the AWS Console
2. Click **Create security group**
3. Fill in the basic details:
   - **Security group name**: `lab05-web-sg`
   - **Description**: `Allow HTTP from anywhere and SSH from my IP`
   - **VPC**: Select your default VPC
4. Under **Inbound rules**, add the following:
   - Rule 1:
     - **Type**: HTTP (port 80)
     - **Source**: Anywhere (0.0.0.0/0)
     - **Description**: Allow HTTP from anywhere
   - Rule 2:
     - **Type**: SSH (port 22)
     - **Source**: My IP
     - **Description**: Allow SSH from my IP
5. Leave outbound rules as default (all traffic allowed)
6. Click **Create security group**

### Step 2: Create a Launch Template

1. Navigate to **EC2 > Launch Templates** in the AWS Console
2. Click **Create launch template**
3. Enter the basic details:
   - **Launch template name**: `lab05-web-template`
   - **Template version description**: `Basic web server with Apache`
   - **Auto Scaling guidance**: Check the box for guidance
4. For **Amazon Machine Image (AMI)**, select Amazon Linux 2 (make sure it's free tier eligible)
5. For **Instance type**, select `t2.micro` (free tier eligible)
6. For **Key pair (login)**, select an existing key pair or create a new one
7. Under **Network settings**, select:
   - **Security groups**: Select the `lab05-web-sg` you created earlier
   - Click on **Advanced network configuration**
   - Click on **Add network interface**
   - Under the network interface settings, set **Auto-assign public IP** to **Enable**
   - **Why this matters**: Your EC2 instances need public IPs to access the internet for installing packages and receiving traffic from the load balancer. Without a public IP, your instances would need a NAT Gateway to access the internet, which adds complexity and cost.
8. Expand the **Advanced details** section
9. Scroll down to **User data** and paste the following script:
   ```bash
   #!/bin/bash
   # Install Apache and start service
   yum update -y
   yum install -y httpd
   
   # Start and enable Apache
   systemctl start httpd
   systemctl enable httpd
   
   # Get instance metadata
   TOKEN=$(curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600")
   INSTANCE_ID=$(curl -H "X-aws-ec2-metadata-token: $TOKEN" http://169.254.169.254/latest/meta-data/instance-id)
   AVAILABILITY_ZONE=$(curl -H "X-aws-ec2-metadata-token: $TOKEN" http://169.254.169.254/latest/meta-data/placement/availability-zone)
   
   # Create a custom index page
   cat <<EOF > /var/www/html/index.html
   <!DOCTYPE html>
   <html>
   <head>
       <title>AWS Load Balancing Lab</title>
       <style>
           body {
               font-family: Arial, sans-serif;
               margin: 0;
               padding: 30px;
               background-color: #f8f9fa;
               color: #333;
           }
           .container {
               max-width: 800px;
               margin: 0 auto;
               background-color: #fff;
               padding: 30px;
               border-radius: 10px;
               box-shadow: 0 2px 10px rgba(0,0,0,0.1);
           }
           h1 {
               color: #0066cc;
               border-bottom: 1px solid #eee;
               padding-bottom: 10px;
           }
           .instance-info {
               background-color: #e9f7ff;
               padding: 15px;
               border-radius: 5px;
               margin-top: 20px;
           }
       </style>
   </head>
   <body>
       <div class="container">
           <h1>Success! Auto Scaling and Load Balancing Lab</h1>
           <p>This page was served from an EC2 instance in an Auto Scaling Group behind a Load Balancer.</p>
           <div class="instance-info">
               <p><strong>Instance ID:</strong> $INSTANCE_ID</p>
               <p><strong>Availability Zone:</strong> $AVAILABILITY_ZONE</p>
               <p><strong>Date and Time:</strong> <span id="datetime"></span></p>
           </div>
           <p>Refresh this page to potentially see a different instance serving your request!</p>
       </div>
       <script>
           document.getElementById("datetime").textContent = new Date().toLocaleString();
       </script>
   </body>
   </html>
   EOF
   ```
10. Click **Create launch template**

## 🚦 Part 2: Create a Target Group and Application Load Balancer

The Application Load Balancer (ALB) distributes incoming traffic to your EC2 instances. Before creating the ALB, we need to create a Target Group which defines where to route the traffic.

### Step 1: Create a Target Group

1. Navigate to **EC2 > Target Groups** in the AWS Console
2. Click **Create target group**
3. Configure basic target group parameters:
   - **Choose a target type**: Instances
   - **Target group name**: `lab05-web-tg`
   - **Protocol**: HTTP
   - **Port**: 80
   - **VPC**: Select your default VPC
   - **Protocol version**: HTTP1
4. Under **Health checks**:
   - **Health check protocol**: HTTP
   - **Health check path**: `/`
   - **Advanced health check settings** (expand):
     - **Port**: traffic port
     - **Healthy threshold**: 2
     - **Unhealthy threshold**: 2
     - **Timeout**: 5 seconds
     - **Interval**: 10 seconds
     - **Success codes**: 200
5. Click **Next**
6. Skip adding targets now (our Auto Scaling Group will register targets automatically)
7. Click **Create target group**

### Step 2: Create an Application Load Balancer

1. Navigate to **EC2 > Load Balancers** in the AWS Console
2. Click **Create load balancer**
3. Choose **Application Load Balancer**
4. Configure the basic load balancer settings:
   - **Load balancer name**: `lab05-web-alb`
   - **Scheme**: Internet-facing
   - **IP address type**: IPv4
5. Under **Network mapping**:
   - **VPC**: Select your default VPC
   - **Mappings**: Select at least two Availability Zones and select a public subnet for each
6. For **Security groups**:
   - **Security groups**: Create a new security group or select an existing one that allows HTTP (port 80) traffic
   - If creating a new one, name it `lab05-alb-sg` and ensure it allows inbound HTTP traffic
7. Under **Listeners and routing**:
   - **Protocol**: HTTP
   - **Port**: 80
   - **Default action**: Forward to target group `lab05-web-tg`
8. Under **Attributes** (optional):
   - Keep all default settings
9. Click **Create load balancer**
10. Wait for the load balancer to be provisioned (this may take a few minutes)

## 📈 Part 3: Create an Auto Scaling Group

The Auto Scaling Group will manage EC2 instances based on your scaling policies, ensuring you have the right number of instances available to handle the load.

### Step 1: Create an Auto Scaling Group

1. Navigate to **EC2 > Auto Scaling Groups** in the AWS Console
2. Click **Create Auto Scaling group**
3. Choose **launch template or configuration**
   - **Auto Scaling group name**: `lab05-web-asg`
   - **Launch template**: Select `lab05-web-template` you created earlier
   - **Version**: Latest 
   - Click **Next**
4. Choose **instance launch options**
   - **VPC**: Select your default VPC
   - **Availability Zones and subnets**: Select at least two Availability Zones
   - Click **Next**
5. **Configure advanced options**
   - **Load balancing**:
     - Select **Attach to an existing load balancer**
     - Select **Choose from your load balancer target groups**
     - Select `lab05-web-tg`
   - **Health checks**:
     - Enable **ELB** health checks
     - **Health check grace period**: 120 seconds
   - Click **Next**
6. **Configure group size and scaling policies**
   - **Desired capacity**: 2
   - **Minimum capacity**: 1
   - **Maximum capacity**: 4
   - Under **Scaling policies**, select **Target tracking scaling policy**
     - **Scaling policy name**: `lab05-cpu-scaling`
     - **Metric type**: Average CPU utilization
     - **Target value**: 50
     - **Instance warmup**: 300 seconds
   - Click **Next**
7. **Add notifications** (optional)
   - Skip for this lab
   - Click **Next**
8. **Add tags** (optional)
   - Add a tag with Key: `Name`, Value: `lab05-web-instance`
   - This tag will be applied to all instances created by the ASG
   - Click **Next**
9. **Review**
   - Review all your settings
   - Click **Create Auto Scaling group**

### Step 2: Monitor Your Auto Scaling Group

1. After creating the ASG, it will start launching instances to meet your desired capacity
2. Navigate to **EC2 > Instances** to see the new instances being launched
3. Navigate to **EC2 > Auto Scaling Groups** and select your ASG
4. Go to the **Activity** tab to monitor the scaling activities
5. Go to the **Instance management** tab to see the instances managed by your ASG

## 🧪 Part 4: Test Your Load Balancer and Auto Scaling Group

Now it's time to verify that your load balancer is distributing traffic and your auto scaling group is working correctly.

### Step 1: Access Your Load Balanced Website

1. Go to **EC2 > Load Balancers** and select your `lab05-web-alb`
2. Find the **DNS name** (e.g., `lab05-web-alb-1234567890.us-east-1.elb.amazonaws.com`)
3. Open a new browser tab and paste the DNS name
4. You should see your web page with instance details
5. Refresh the page several times - you should notice the Instance ID and AZ changing as the load balancer routes requests to different instances

### Step 2: Test Auto Scaling (Optional Simulation)

To test auto scaling in a lab environment, we can manually adjust the desired capacity:

1. Navigate to your auto scaling group
2. Click **Edit**
3. Change **Desired capacity** to 3
4. Click **Update**
5. Go back to the **Activity** tab and watch as a new instance is launched
6. Check **EC2 > Instances** to see the new instance appear

### Step 3: Test Instance Health Checks (Optional)

1. Connect to one of your instances via SSH:
   ```bash
   ssh -i your-key.pem ec2-user@instance-public-ip
   ```
2. Stop the web server to simulate a failure:
   ```bash
   sudo systemctl stop httpd
   ```
3. After a few minutes, observe the Auto Scaling group activity:
   - The unhealthy instance should be terminated
   - A new instance should be launched to replace it

## 💻 Part 5: Create the Same Setup Using AWS CLI

Let's replicate the above setup using AWS CLI commands. These commands are useful for automation and infrastructure as code.

### Step 1: Create a Security Group for Web Servers

```bash
# Create security group
SG_ID=$(aws ec2 create-security-group \
  --group-name lab05-cli-web-sg \
  --description "Allow HTTP and SSH access" \
  --vpc-id YOUR_VPC_ID \
  --query GroupId \
  --output text)

echo "Security Group created with ID: $SG_ID"

# Add HTTP rule
aws ec2 authorize-security-group-ingress \
  --group-id $SG_ID \
  --protocol tcp \
  --port 80 \
  --cidr 0.0.0.0/0

# Add SSH rule - replace YOUR_IP with your IP address
aws ec2 authorize-security-group-ingress \
  --group-id $SG_ID \
  --protocol tcp \
  --port 22 \
  --cidr YOUR_IP/32
```

### Step 2: Create a Launch Template

First, create a file named `user-data.sh` with the web server script from the previous section. Then:

```bash
# Create launch template
TEMPLATE_ID=$(aws ec2 create-launch-template \
  --launch-template-name lab05-cli-web-template \
  --version-description "Web server template" \
  --launch-template-data '{
    "ImageId": "ami-0c55b159cbfafe1f0",
    "InstanceType": "t2.micro",
    "KeyName": "YOUR_KEY_NAME",
    "UserData": "'$(base64 -w 0 user-data.sh)'",
    "NetworkInterfaces": [{
      "DeviceIndex": 0,
      "AssociatePublicIpAddress": true,
      "Groups": ["'$SG_ID'"]
    }]
  }' \
  --query LaunchTemplate.LaunchTemplateId \
  --output text)

echo "Launch Template created with ID: $TEMPLATE_ID"
```

### Step 3: Create a Target Group

```bash
# Create target group
TG_ARN=$(aws elbv2 create-target-group \
  --name lab05-cli-web-tg \
  --protocol HTTP \
  --port 80 \
  --vpc-id YOUR_VPC_ID \
  --health-check-protocol HTTP \
  --health-check-path "/" \
  --health-check-interval-seconds 10 \
  --health-check-timeout-seconds 5 \
  --healthy-threshold-count 2 \
  --unhealthy-threshold-count 2 \
  --query TargetGroups[0].TargetGroupArn \
  --output text)

echo "Target Group created with ARN: $TG_ARN"
```

### Step 4: Create a Load Balancer

```bash
# Create load balancer
LB_ARN=$(aws elbv2 create-load-balancer \
  --name lab05-cli-web-alb \
  --subnets subnet-xxxx subnet-yyyy \
  --security-groups $SG_ID \
  --query LoadBalancers[0].LoadBalancerArn \
  --output text)

echo "Load Balancer created with ARN: $LB_ARN"

# Create listener
LISTENER_ARN=$(aws elbv2 create-listener \
  --load-balancer-arn $LB_ARN \
  --protocol HTTP \
  --port 80 \
  --default-actions Type=forward,TargetGroupArn=$TG_ARN \
  --query Listeners[0].ListenerArn \
  --output text)

echo "Listener created with ARN: $LISTENER_ARN"
```

### Step 5: Create an Auto Scaling Group

```bash
# Create auto scaling group
aws autoscaling create-auto-scaling-group \
  --auto-scaling-group-name lab05-cli-web-asg \
  --launch-template LaunchTemplateId=$TEMPLATE_ID,Version='$Latest' \
  --min-size 1 \
  --max-size 4 \
  --desired-capacity 2 \
  --vpc-zone-identifier "subnet-xxxx,subnet-yyyy" \
  --target-group-arns $TG_ARN \
  --health-check-type ELB \
  --health-check-grace-period 120

echo "Auto Scaling Group created"

# Create scaling policy
aws autoscaling put-scaling-policy \
  --auto-scaling-group-name lab05-cli-web-asg \
  --policy-name lab05-cli-cpu-scaling \
  --policy-type TargetTrackingScaling \
  --target-tracking-configuration '{
    "PredefinedMetricSpecification": {
      "PredefinedMetricType": "ASGAverageCPUUtilization"
    },
    "TargetValue": 50.0,
    "DisableScaleIn": false
  }'

echo "Scaling policy created"
```

## 🔍 Troubleshooting Common Issues

### Load Balancer Health Check Failing
- Ensure security groups allow traffic from the load balancer to instances on port 80
- Verify the web server is running on instances (`systemctl status httpd`)
- Check the health check path exists and returns a 200 status code

### Instances Not Launching
- Check activity history in the Auto Scaling Group console
- Verify the launch template has valid AMI ID and security group
- Ensure IAM roles and permissions are correct

### Load Balancer Not Accessible
- Verify the load balancer security group allows inbound traffic on port 80
- Check that the load balancer is in an "active" state
- Ensure the target group has registered targets that are healthy

### Instances Cannot Install Software or Access Internet
- Verify instances have public IPs assigned (check the EC2 console)
- If you didn't enable auto-assign public IP in the launch template, your instances won't have internet access
- Alternatively, check if your VPC has a NAT Gateway configured for private subnets

## 🌀 Cleanup - IMPORTANT!

To avoid ongoing charges, clean up all resources:

### Console Cleanup:
1. Delete the Auto Scaling group
   - This will terminate all instances managed by the ASG
2. Delete the Load Balancer
3. Delete the Target Group
4. Delete the Launch Template
5. Delete the Security Groups created for this lab

### CLI Cleanup:

```bash
# Delete Auto Scaling Group
aws autoscaling delete-auto-scaling-group \
  --auto-scaling-group-name lab05-cli-web-asg \
  --force-delete

# Wait for ASG to delete
echo "Waiting for Auto Scaling Group to delete..."
sleep 60

# Delete Load Balancer
aws elbv2 delete-load-balancer \
  --load-balancer-arn $LB_ARN

# Delete Target Group
aws elbv2 delete-target-group \
  --target-group-arn $TG_ARN

# Delete Launch Template
aws ec2 delete-launch-template \
  --launch-template-id $TEMPLATE_ID

# Delete Security Group
aws ec2 delete-security-group \
  --group-id $SG_ID
```

## ✅ What You've Learned

Congratulations! In this lab, you have:

1. Created a Launch Template with a custom web server configuration
2. Set up an Application Load Balancer to distribute traffic
3. Configured a Target Group with health checks to monitor instance health
4. Deployed an Auto Scaling Group that automatically maintains instance capacity
5. Implemented a scaling policy to adjust capacity based on CPU utilization
6. Tested load balancing by observing traffic distribution
7. Learned how to create all these resources via both the AWS Console and CLI

These skills are essential for building production-ready applications that can handle varying loads and maintain high availability. The architecture you've built forms the foundation of modern, resilient cloud applications.

## 📚 Additional Resources

- [Application Load Balancer Documentation](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/introduction.html)
- [Auto Scaling Group Documentation](https://docs.aws.amazon.com/autoscaling/ec2/userguide/what-is-amazon-ec2-auto-scaling.html)
- [EC2 Launch Templates](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-launch-templates.html)
- [AWS Elastic Load Balancing](https://aws.amazon.com/elasticloadbalancing/)
- [EC2 Auto Scaling Documentation](https://aws.amazon.com/ec2/autoscaling/)

---

Happy Clouding ☁️!

