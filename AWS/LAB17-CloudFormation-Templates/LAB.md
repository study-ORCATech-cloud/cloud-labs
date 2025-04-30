# LAB17: Infrastructure as Code with AWS CloudFormation

## ⚡ Lab Overview

In this comprehensive lab, you'll work with **AWS CloudFormation**, a powerful service that helps you model and set up your AWS resources. With CloudFormation, you can use a template file to create and delete a collection of resources together as a single unit (a stack). This approach to infrastructure management is commonly referred to as "Infrastructure as Code" (IaC).

### 💼 Real-World DevOps Applications

CloudFormation is a fundamental tool in AWS-based DevOps practices and offers several key advantages:

- **Consistency**: Deploy the same infrastructure repeatedly with consistent results
- **Version Control**: Infrastructure templates can be version-controlled like application code
- **Automation**: Eliminate manual processes for provisioning resources
- **Documentation**: Templates serve as documentation of your infrastructure
- **Scalability**: Easily replicate environments for development, testing, and production
- **Integrated Management**: Create, update, or delete resources as a single unit

Common production use cases include:

- **Application Infrastructure**: Deploying complete application stacks with compute, storage, and networking
- **Multi-Region Deployments**: Replicating the same infrastructure across regions
- **Environment Provisioning**: Creating development, testing, staging, and production environments
- **Disaster Recovery**: Setting up backup infrastructure for business continuity
- **Compliance Enforcement**: Ensuring infrastructure meets organizational policies and standards
- **Resource Documentation**: Using templates as living documentation of infrastructure

---

## 🎯 Objectives

By the end of this lab, you will be able to:
- Create CloudFormation templates in both YAML and JSON formats
- Deploy a CloudFormation stack using the AWS console
- Update an existing stack with new resources
- Use parameters to make templates reusable across different environments
- Create outputs to easily access important resource information
- Use the AWS CLI to deploy and manage CloudFormation stacks
- Validate templates before deployment
- Create and use change sets to preview changes to stacks
- Delete stacks and understand the resource cleanup process

---

## 🛠️ Prerequisites

- AWS account with administrative permissions
- Basic understanding of AWS services (EC2, VPC, S3)
- Familiarity with YAML and/or JSON
- AWS CLI installed and configured (refer to [CLI Setup Guide](../../CLI-Setup.md))

---

# 🌐 PART 1: AWS MANAGEMENT CONSOLE IMPLEMENTATION

In this first part of the lab, you'll create and deploy a CloudFormation template using the AWS Management Console.

## 📊 Step 1: Create Your First CloudFormation Template

Let's create a simple template that will create an S3 bucket:

1. Open a text editor of your choice
2. Copy and paste the following YAML template:

```yaml
AWSTemplateFormatVersion: '2010-09-09'
Description: 'A simple CloudFormation template that creates an S3 bucket'

Resources:
  MyS3Bucket:
    Type: 'AWS::S3::Bucket'
    Properties:
      BucketName: !Join 
        - '-'
        - - 'lab17-cloudformation'
          - !Select 
            - 0
            - !Split 
              - '-'
              - !Select 
                - 2
                - !Split 
                  - '/'
                  - !Ref 'AWS::StackId'
      AccessControl: Private
      VersioningConfiguration:
        Status: Enabled
      Tags:
        - Key: Purpose
          Value: CloudFormationLab
```

3. Save the file as `simple-s3-bucket.yaml`

This template uses intrinsic functions to generate a unique bucket name by using part of the stack ID.

## 🚀 Step 2: Deploy the Template via the Console

1. Sign in to the AWS Management Console
2. Navigate to the **CloudFormation** service
3. Click **Create stack** > **With new resources (standard)**
4. In the **Specify template** section, select **Upload a template file**
5. Click **Choose file** and select your `simple-s3-bucket.yaml` file
6. Click **Next**
7. For **Stack name**, enter `lab17-s3-stack`
8. Click **Next**
9. On the **Configure stack options** page:
   - Under **Tags**, add a tag with key `Lab` and value `CloudFormation`
   - Leave other settings as default
10. Click **Next**
11. Review the stack details and click **Create stack**

## 🔄 Step 3: Monitor Stack Creation

1. You will be taken to the stack details page
2. Click on the **Events** tab to see the progress of resource creation
3. Wait until the stack status changes to **CREATE_COMPLETE**
4. Click on the **Resources** tab to see the resources that were created
5. Note the physical ID of the S3 bucket that was created

## 🔍 Step 4: Explore Stack Outputs

Now, let's modify our template to include outputs:

1. Open your `simple-s3-bucket.yaml` file
2. Add the following section at the end:

```yaml
Outputs:
  BucketName:
    Description: Name of the S3 bucket
    Value: !Ref MyS3Bucket
  BucketARN:
    Description: ARN of the S3 bucket
    Value: !GetAtt MyS3Bucket.Arn
```

3. Save the file

## 🛠️ Step 5: Update the Stack

1. In the CloudFormation console, select your `lab17-s3-stack`
2. Click **Update**
3. Select **Replace current template**
4. Choose **Upload a template file** and upload your updated template
5. Click **Next** through the pages, keeping the same settings
6. On the review page, click **Update stack**
7. Monitor the update process in the **Events** tab
8. Once the update is complete, click on the **Outputs** tab to see your newly added outputs

## 📊 Step 6: Create a More Complex Template

Now let's create a more comprehensive template that deploys a VPC with public and private subnets, an EC2 instance, and security groups:

1. Create a new file in your text editor
2. Copy and paste the following YAML template:

```yaml
AWSTemplateFormatVersion: '2010-09-09'
Description: 'CloudFormation template for a VPC with public/private subnets and an EC2 instance'

Parameters:
  EnvironmentName:
    Description: Environment name prefix for resources
    Type: String
    Default: lab17
  
  InstanceType:
    Description: EC2 instance type
    Type: String
    Default: t2.micro
    AllowedValues:
      - t2.micro
      - t3.micro
    ConstraintDescription: Must be a valid EC2 instance type.

Resources:
  # VPC Definition
  VPC:
    Type: AWS::EC2::VPC
    Properties:
      CidrBlock: 10.0.0.0/16
      EnableDnsSupport: true
      EnableDnsHostnames: true
      Tags:
        - Key: Name
          Value: !Sub ${EnvironmentName}-vpc

  # Internet Gateway
  InternetGateway:
    Type: AWS::EC2::InternetGateway
    Properties:
      Tags:
        - Key: Name
          Value: !Sub ${EnvironmentName}-igw

  InternetGatewayAttachment:
    Type: AWS::EC2::VPCGatewayAttachment
    Properties:
      InternetGatewayId: !Ref InternetGateway
      VpcId: !Ref VPC

  # Public Subnet
  PublicSubnet:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId: !Ref VPC
      AvailabilityZone: !Select [ 0, !GetAZs '' ]
      CidrBlock: 10.0.1.0/24
      MapPublicIpOnLaunch: true
      Tags:
        - Key: Name
          Value: !Sub ${EnvironmentName}-public-subnet

  # Public Route Table
  PublicRouteTable:
    Type: AWS::EC2::RouteTable
    Properties:
      VpcId: !Ref VPC
      Tags:
        - Key: Name
          Value: !Sub ${EnvironmentName}-public-routes

  DefaultPublicRoute:
    Type: AWS::EC2::Route
    DependsOn: InternetGatewayAttachment
    Properties:
      RouteTableId: !Ref PublicRouteTable
      DestinationCidrBlock: 0.0.0.0/0
      GatewayId: !Ref InternetGateway

  PublicSubnetRouteTableAssociation:
    Type: AWS::EC2::SubnetRouteTableAssociation
    Properties:
      RouteTableId: !Ref PublicRouteTable
      SubnetId: !Ref PublicSubnet

  # Private Subnet
  PrivateSubnet:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId: !Ref VPC
      AvailabilityZone: !Select [ 0, !GetAZs '' ]
      CidrBlock: 10.0.2.0/24
      MapPublicIpOnLaunch: false
      Tags:
        - Key: Name
          Value: !Sub ${EnvironmentName}-private-subnet

  # Private Route Table
  PrivateRouteTable:
    Type: AWS::EC2::RouteTable
    Properties:
      VpcId: !Ref VPC
      Tags:
        - Key: Name
          Value: !Sub ${EnvironmentName}-private-routes

  PrivateSubnetRouteTableAssociation:
    Type: AWS::EC2::SubnetRouteTableAssociation
    Properties:
      RouteTableId: !Ref PrivateRouteTable
      SubnetId: !Ref PrivateSubnet

  # Security Group
  WebServerSecurityGroup:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription: Security group for web server
      VpcId: !Ref VPC
      SecurityGroupIngress:
        - IpProtocol: tcp
          FromPort: 80
          ToPort: 80
          CidrIp: 0.0.0.0/0
        - IpProtocol: tcp
          FromPort: 22
          ToPort: 22
          CidrIp: 0.0.0.0/0
      Tags:
        - Key: Name
          Value: !Sub ${EnvironmentName}-webserver-sg

  # EC2 Instance
  WebServerInstance:
    Type: AWS::EC2::Instance
    Properties:
      InstanceType: !Ref InstanceType
      SecurityGroupIds:
        - !Ref WebServerSecurityGroup
      SubnetId: !Ref PublicSubnet
      ImageId: '{{resolve:ssm:/aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-gp2}}'
      UserData:
        Fn::Base64: !Sub |
          #!/bin/bash -xe
          yum update -y
          yum install -y httpd
          echo "<h1>Hello from CloudFormation!</h1><p>Stack name: ${AWS::StackName}</p>" > /var/www/html/index.html
          systemctl start httpd
          systemctl enable httpd
      Tags:
        - Key: Name
          Value: !Sub ${EnvironmentName}-webserver

Outputs:
  VPC:
    Description: A reference to the created VPC
    Value: !Ref VPC

  PublicSubnet:
    Description: A reference to the public subnet
    Value: !Ref PublicSubnet

  PrivateSubnet:
    Description: A reference to the private subnet
    Value: !Ref PrivateSubnet

  WebServerPublicIP:
    Description: Public IP address of the web server
    Value: !GetAtt WebServerInstance.PublicIp

  WebServerURL:
    Description: URL of the web server
    Value: !Sub http://${WebServerInstance.PublicDnsName}
```

3. Save the file as `vpc-ec2-template.yaml`

## 🚀 Step 7: Deploy the Complex Template

1. In the CloudFormation console, click **Create stack** > **With new resources (standard)**
2. Upload your `vpc-ec2-template.yaml` file
3. Click **Next**
4. For **Stack name**, enter `lab17-vpc-ec2-stack`
5. You can leave the parameters with their default values or change them if you want
6. Click **Next**
7. On the **Configure stack options** page, add tags as before
8. Click **Next**
9. Review and click **Create stack**

## 🔍 Step 8: Explore Stack Resources and Outputs

1. Monitor the stack creation in the **Events** tab
2. Once the stack is in the **CREATE_COMPLETE** state, explore the **Resources** tab to see all the resources created
3. Click on the **Outputs** tab and copy the **WebServerURL** value
4. Paste the URL into a browser to see your web server running

## 📝 Step 9: Create a Change Set

Let's modify our VPC template to add a second EC2 instance:

1. Open your `vpc-ec2-template.yaml` file
2. Add the following resource after the `WebServerInstance` section:

```yaml
  SecondWebServerInstance:
    Type: AWS::EC2::Instance
    Properties:
      InstanceType: !Ref InstanceType
      SecurityGroupIds:
        - !Ref WebServerSecurityGroup
      SubnetId: !Ref PublicSubnet
      ImageId: '{{resolve:ssm:/aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-gp2}}'
      UserData:
        Fn::Base64: !Sub |
          #!/bin/bash -xe
          yum update -y
          yum install -y httpd
          echo "<h1>Hello from Second Instance!</h1><p>Stack name: ${AWS::StackName}</p>" > /var/www/html/index.html
          systemctl start httpd
          systemctl enable httpd
      Tags:
        - Key: Name
          Value: !Sub ${EnvironmentName}-webserver-2
```

3. Also add a new output:

```yaml
  SecondWebServerURL:
    Description: URL of the second web server
    Value: !Sub http://${SecondWebServerInstance.PublicDnsName}
```

4. Save the file

## 🔄 Step 10: Preview Changes Using a Change Set

1. In the CloudFormation console, select your `lab17-vpc-ec2-stack`
2. Click **Stack actions** > **Create change set for current stack**
3. Select **Replace current template**
4. Upload your updated template and click **Next**
5. Keep the parameters the same and click **Next**
6. Keep the stack options the same and click **Next**
7. On the review page, enter a change set name like `add-second-instance`
8. Click **Create change set**
9. Wait for the change set to be created, then review the changes
10. You should see that a new EC2 instance will be added
11. Click **Execute change set**
12. Wait for the stack update to complete
13. Once complete, go to the **Outputs** tab and check the URL of the second web server

## 🛠️ Step 11: Use CloudFormation Designer

1. In the CloudFormation console, click on **Templates** in the left navigation
2. Click on your `lab17-vpc-ec2-stack` stack
3. Click **View in Designer**
4. Explore the graphical representation of your template
5. You can see how resources are connected and their relationships
6. Try modifying the template in the integrated editor (bottom panel)
7. You can also download the template for future use

## 🧹 Step 12: Console Implementation Cleanup

To avoid ongoing charges, let's clean up the resources created:

1. In the CloudFormation console, select your `lab17-vpc-ec2-stack` stack
2. Click **Delete**
3. Confirm the deletion by clicking **Delete stack**
4. Monitor the deletion process in the **Events** tab
5. Repeat the process for your `lab17-s3-stack`
6. Wait until both stacks are completely deleted

Note: CloudFormation will automatically delete all resources that were created by the stack in the correct order, handling dependencies appropriately. This is one of the key benefits of using Infrastructure as Code.

---

# 🖥️ PART 2: AWS CLI IMPLEMENTATION

In this second part of the lab, you'll perform similar operations using the AWS Command Line Interface (CLI).

## 📊 Step 1: Create the Simple S3 Template (CLI)

Let's create the simple S3 bucket template similar to the one we used in the console part:

```bash
# Create a directory for your CloudFormation templates
mkdir -p ~/cloudformation-lab
cd ~/cloudformation-lab

# Create the simple S3 bucket template
cat > simple-s3-bucket-cli.yaml << 'EOL'
AWSTemplateFormatVersion: '2010-09-09'
Description: 'A simple CloudFormation template that creates an S3 bucket (CLI version)'

Resources:
  MyS3Bucket:
    Type: 'AWS::S3::Bucket'
    Properties:
      BucketName: !Join 
        - '-'
        - - 'lab17-cf-cli'
          - !Select 
            - 0
            - !Split 
              - '-'
              - !Select 
                - 2
                - !Split 
                  - '/'
                  - !Ref 'AWS::StackId'
      AccessControl: Private
      VersioningConfiguration:
        Status: Enabled
      Tags:
        - Key: Purpose
          Value: CloudFormationCliLab

Outputs:
  BucketName:
    Description: Name of the S3 bucket
    Value: !Ref MyS3Bucket
  BucketARN:
    Description: ARN of the S3 bucket
    Value: !GetAtt MyS3Bucket.Arn
EOL

echo "S3 bucket template created"
```

## 🔍 Step 2: Validate the Template (CLI)

Before deploying, let's validate the template to ensure it's correctly formatted:

```bash
# Validate the template
aws cloudformation validate-template --template-body file://simple-s3-bucket-cli.yaml

echo "Template validation complete"
```

## 🚀 Step 3: Deploy the Stack (CLI)

Now let's deploy the stack using the AWS CLI:

```bash
# Deploy the stack
aws cloudformation create-stack \
  --stack-name lab17-s3-stack-cli \
  --template-body file://simple-s3-bucket-cli.yaml \
  --tags Key=Lab,Value=CloudFormationCLI

echo "Stack creation initiated, waiting for completion..."

# Wait for the stack to complete
aws cloudformation wait stack-create-complete --stack-name lab17-s3-stack-cli

echo "Stack creation complete!"
```

## 🔄 Step 4: Describe the Stack (CLI)

Let's check the details of our deployed stack:

```bash
# Get basic stack information
aws cloudformation describe-stacks --stack-name lab17-s3-stack-cli

# List stack resources
aws cloudformation list-stack-resources --stack-name lab17-s3-stack-cli

# Get stack outputs
aws cloudformation describe-stacks --stack-name lab17-s3-stack-cli --query 'Stacks[0].Outputs'

echo "Stack information retrieved"
```

## 📊 Step 5: Create the Complex VPC-EC2 Template (CLI)

Now let's create the more complex template for VPC and EC2:

```bash
# Create the complex VPC-EC2 template
cat > vpc-ec2-template-cli.yaml << 'EOL'
AWSTemplateFormatVersion: '2010-09-09'
Description: 'CloudFormation template for a VPC with public/private subnets and an EC2 instance (CLI version)'

Parameters:
  EnvironmentName:
    Description: Environment name prefix for resources
    Type: String
    Default: lab17-cli
  
  InstanceType:
    Description: EC2 instance type
    Type: String
    Default: t2.micro
    AllowedValues:
      - t2.micro
      - t3.micro
    ConstraintDescription: Must be a valid EC2 instance type.

Resources:
  # VPC Definition
  VPC:
    Type: AWS::EC2::VPC
    Properties:
      CidrBlock: 10.0.0.0/16
      EnableDnsSupport: true
      EnableDnsHostnames: true
      Tags:
        - Key: Name
          Value: !Sub ${EnvironmentName}-vpc

  # Internet Gateway
  InternetGateway:
    Type: AWS::EC2::InternetGateway
    Properties:
      Tags:
        - Key: Name
          Value: !Sub ${EnvironmentName}-igw

  InternetGatewayAttachment:
    Type: AWS::EC2::VPCGatewayAttachment
    Properties:
      InternetGatewayId: !Ref InternetGateway
      VpcId: !Ref VPC

  # Public Subnet
  PublicSubnet:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId: !Ref VPC
      AvailabilityZone: !Select [ 0, !GetAZs '' ]
      CidrBlock: 10.0.1.0/24
      MapPublicIpOnLaunch: true
      Tags:
        - Key: Name
          Value: !Sub ${EnvironmentName}-public-subnet

  # Public Route Table
  PublicRouteTable:
    Type: AWS::EC2::RouteTable
    Properties:
      VpcId: !Ref VPC
      Tags:
        - Key: Name
          Value: !Sub ${EnvironmentName}-public-routes

  DefaultPublicRoute:
    Type: AWS::EC2::Route
    DependsOn: InternetGatewayAttachment
    Properties:
      RouteTableId: !Ref PublicRouteTable
      DestinationCidrBlock: 0.0.0.0/0
      GatewayId: !Ref InternetGateway

  PublicSubnetRouteTableAssociation:
    Type: AWS::EC2::SubnetRouteTableAssociation
    Properties:
      RouteTableId: !Ref PublicRouteTable
      SubnetId: !Ref PublicSubnet

  # Private Subnet
  PrivateSubnet:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId: !Ref VPC
      AvailabilityZone: !Select [ 0, !GetAZs '' ]
      CidrBlock: 10.0.2.0/24
      MapPublicIpOnLaunch: false
      Tags:
        - Key: Name
          Value: !Sub ${EnvironmentName}-private-subnet

  # Private Route Table
  PrivateRouteTable:
    Type: AWS::EC2::RouteTable
    Properties:
      VpcId: !Ref VPC
      Tags:
        - Key: Name
          Value: !Sub ${EnvironmentName}-private-routes

  PrivateSubnetRouteTableAssociation:
    Type: AWS::EC2::SubnetRouteTableAssociation
    Properties:
      RouteTableId: !Ref PrivateRouteTable
      SubnetId: !Ref PrivateSubnet

  # Security Group
  WebServerSecurityGroup:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription: Security group for web server
      VpcId: !Ref VPC
      SecurityGroupIngress:
        - IpProtocol: tcp
          FromPort: 80
          ToPort: 80
          CidrIp: 0.0.0.0/0
        - IpProtocol: tcp
          FromPort: 22
          ToPort: 22
          CidrIp: 0.0.0.0/0
      Tags:
        - Key: Name
          Value: !Sub ${EnvironmentName}-webserver-sg

  # EC2 Instance
  WebServerInstance:
    Type: AWS::EC2::Instance
    Properties:
      InstanceType: !Ref InstanceType
      SecurityGroupIds:
        - !Ref WebServerSecurityGroup
      SubnetId: !Ref PublicSubnet
      ImageId: '{{resolve:ssm:/aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-gp2}}'
      UserData:
        Fn::Base64: !Sub |
          #!/bin/bash -xe
          yum update -y
          yum install -y httpd
          echo "<h1>Hello from CloudFormation CLI!</h1><p>Stack name: ${AWS::StackName}</p>" > /var/www/html/index.html
          systemctl start httpd
          systemctl enable httpd
      Tags:
        - Key: Name
          Value: !Sub ${EnvironmentName}-webserver

Outputs:
  VPC:
    Description: A reference to the created VPC
    Value: !Ref VPC

  PublicSubnet:
    Description: A reference to the public subnet
    Value: !Ref PublicSubnet

  PrivateSubnet:
    Description: A reference to the private subnet
    Value: !Ref PrivateSubnet

  WebServerPublicIP:
    Description: Public IP address of the web server
    Value: !GetAtt WebServerInstance.PublicIp

  WebServerURL:
    Description: URL of the web server
    Value: !Sub http://${WebServerInstance.PublicDnsName}
EOL

echo "VPC-EC2 template created"
```

## 🔍 Step 6: Create Parameter File (CLI)

Let's create a parameter file for our stack:

```bash
# Create parameters file
cat > parameters.json << 'EOL'
[
  {
    "ParameterKey": "EnvironmentName",
    "ParameterValue": "lab17-cli"
  },
  {
    "ParameterKey": "InstanceType",
    "ParameterValue": "t2.micro"
  }
]
EOL

echo "Parameters file created"
```

## 🚀 Step 7: Deploy the Complex Stack (CLI)

Now let's deploy our complex stack:

```bash
# Validate the complex template
aws cloudformation validate-template --template-body file://vpc-ec2-template-cli.yaml

# Deploy the complex stack
aws cloudformation create-stack \
  --stack-name lab17-vpc-ec2-stack-cli \
  --template-body file://vpc-ec2-template-cli.yaml \
  --parameters file://parameters.json \
  --capabilities CAPABILITY_IAM \
  --tags Key=Lab,Value=CloudFormationCLI

echo "Complex stack creation initiated, waiting for completion..."

# Wait for the stack to complete (this might take several minutes)
aws cloudformation wait stack-create-complete --stack-name lab17-vpc-ec2-stack-cli

echo "Complex stack creation complete!"
```

## 🔄 Step 8: Get Stack Outputs (CLI)

Let's retrieve and display the outputs from our stack:

```bash
# Get the outputs
WEB_URL=$(aws cloudformation describe-stacks --stack-name lab17-vpc-ec2-stack-cli --query 'Stacks[0].Outputs[?OutputKey==`WebServerURL`].OutputValue' --output text)

echo "Web server is available at: $WEB_URL"
```

## 📝 Step 9: Create a Change Set (CLI)

Let's create an updated template that adds a second EC2 instance:

```bash
# Create updated template with a second EC2 instance
cat > vpc-ec2-template-updated-cli.yaml << 'EOL'
AWSTemplateFormatVersion: '2010-09-09'
Description: 'Updated CloudFormation template with a second EC2 instance (CLI version)'

Parameters:
  EnvironmentName:
    Description: Environment name prefix for resources
    Type: String
    Default: lab17-cli
  
  InstanceType:
    Description: EC2 instance type
    Type: String
    Default: t2.micro
    AllowedValues:
      - t2.micro
      - t3.micro
    ConstraintDescription: Must be a valid EC2 instance type.

Resources:
  # VPC Definition
  VPC:
    Type: AWS::EC2::VPC
    Properties:
      CidrBlock: 10.0.0.0/16
      EnableDnsSupport: true
      EnableDnsHostnames: true
      Tags:
        - Key: Name
          Value: !Sub ${EnvironmentName}-vpc

  # Internet Gateway
  InternetGateway:
    Type: AWS::EC2::InternetGateway
    Properties:
      Tags:
        - Key: Name
          Value: !Sub ${EnvironmentName}-igw

  InternetGatewayAttachment:
    Type: AWS::EC2::VPCGatewayAttachment
    Properties:
      InternetGatewayId: !Ref InternetGateway
      VpcId: !Ref VPC

  # Public Subnet
  PublicSubnet:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId: !Ref VPC
      AvailabilityZone: !Select [ 0, !GetAZs '' ]
      CidrBlock: 10.0.1.0/24
      MapPublicIpOnLaunch: true
      Tags:
        - Key: Name
          Value: !Sub ${EnvironmentName}-public-subnet

  # Public Route Table
  PublicRouteTable:
    Type: AWS::EC2::RouteTable
    Properties:
      VpcId: !Ref VPC
      Tags:
        - Key: Name
          Value: !Sub ${EnvironmentName}-public-routes

  DefaultPublicRoute:
    Type: AWS::EC2::Route
    DependsOn: InternetGatewayAttachment
    Properties:
      RouteTableId: !Ref PublicRouteTable
      DestinationCidrBlock: 0.0.0.0/0
      GatewayId: !Ref InternetGateway

  PublicSubnetRouteTableAssociation:
    Type: AWS::EC2::SubnetRouteTableAssociation
    Properties:
      RouteTableId: !Ref PublicRouteTable
      SubnetId: !Ref PublicSubnet

  # Private Subnet
  PrivateSubnet:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId: !Ref VPC
      AvailabilityZone: !Select [ 0, !GetAZs '' ]
      CidrBlock: 10.0.2.0/24
      MapPublicIpOnLaunch: false
      Tags:
        - Key: Name
          Value: !Sub ${EnvironmentName}-private-subnet

  # Private Route Table
  PrivateRouteTable:
    Type: AWS::EC2::RouteTable
    Properties:
      VpcId: !Ref VPC
      Tags:
        - Key: Name
          Value: !Sub ${EnvironmentName}-private-routes

  PrivateSubnetRouteTableAssociation:
    Type: AWS::EC2::SubnetRouteTableAssociation
    Properties:
      RouteTableId: !Ref PrivateRouteTable
      SubnetId: !Ref PrivateSubnet

  # Security Group
  WebServerSecurityGroup:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription: Security group for web server
      VpcId: !Ref VPC
      SecurityGroupIngress:
        - IpProtocol: tcp
          FromPort: 80
          ToPort: 80
          CidrIp: 0.0.0.0/0
        - IpProtocol: tcp
          FromPort: 22
          ToPort: 22
          CidrIp: 0.0.0.0/0
      Tags:
        - Key: Name
          Value: !Sub ${EnvironmentName}-webserver-sg

  # EC2 Instance
  WebServerInstance:
    Type: AWS::EC2::Instance
    Properties:
      InstanceType: !Ref InstanceType
      SecurityGroupIds:
        - !Ref WebServerSecurityGroup
      SubnetId: !Ref PublicSubnet
      ImageId: '{{resolve:ssm:/aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-gp2}}'
      UserData:
        Fn::Base64: !Sub |
          #!/bin/bash -xe
          yum update -y
          yum install -y httpd
          echo "<h1>Hello from CloudFormation CLI!</h1><p>Stack name: ${AWS::StackName}</p>" > /var/www/html/index.html
          systemctl start httpd
          systemctl enable httpd
      Tags:
        - Key: Name
          Value: !Sub ${EnvironmentName}-webserver

  # Second EC2 Instance (New)
  SecondWebServerInstance:
    Type: AWS::EC2::Instance
    Properties:
      InstanceType: !Ref InstanceType
      SecurityGroupIds:
        - !Ref WebServerSecurityGroup
      SubnetId: !Ref PublicSubnet
      ImageId: '{{resolve:ssm:/aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-gp2}}'
      UserData:
        Fn::Base64: !Sub |
          #!/bin/bash -xe
          yum update -y
          yum install -y httpd
          echo "<h1>Hello from Second Instance (CLI)!</h1><p>Stack name: ${AWS::StackName}</p>" > /var/www/html/index.html
          systemctl start httpd
          systemctl enable httpd
      Tags:
        - Key: Name
          Value: !Sub ${EnvironmentName}-webserver-2

Outputs:
  VPC:
    Description: A reference to the created VPC
    Value: !Ref VPC

  PublicSubnet:
    Description: A reference to the public subnet
    Value: !Ref PublicSubnet

  PrivateSubnet:
    Description: A reference to the private subnet
    Value: !Ref PrivateSubnet

  WebServerPublicIP:
    Description: Public IP address of the web server
    Value: !GetAtt WebServerInstance.PublicIp

  WebServerURL:
    Description: URL of the web server
    Value: !Sub http://${WebServerInstance.PublicDnsName}
    
  SecondWebServerURL:
    Description: URL of the second web server
    Value: !Sub http://${SecondWebServerInstance.PublicDnsName}
EOL

echo "Updated template created"
```

Now let's create a change set to see what will change:

```bash
# Create a change set
aws cloudformation create-change-set \
  --stack-name lab17-vpc-ec2-stack-cli \
  --change-set-name add-second-instance-cli \
  --template-body file://vpc-ec2-template-updated-cli.yaml \
  --parameters file://parameters.json \
  --capabilities CAPABILITY_IAM

echo "Change set created, waiting for it to be ready..."

# Wait for the change set to be created
sleep 15

# Describe the change set
aws cloudformation describe-change-set \
  --stack-name lab17-vpc-ec2-stack-cli \
  --change-set-name add-second-instance-cli

echo "Change set details retrieved"
```

## 🔄 Step 10: Execute Change Set (CLI)

Now let's execute the change set to apply our changes:

```bash
# Execute the change set
aws cloudformation execute-change-set \
  --stack-name lab17-vpc-ec2-stack-cli \
  --change-set-name add-second-instance-cli

echo "Change set execution initiated, waiting for completion..."

# Wait for the stack update to complete
aws cloudformation wait stack-update-complete --stack-name lab17-vpc-ec2-stack-cli

echo "Stack update complete!"

# Get the new outputs
WEB_URL_2=$(aws cloudformation describe-stacks --stack-name lab17-vpc-ec2-stack-cli --query 'Stacks[0].Outputs[?OutputKey==`SecondWebServerURL`].OutputValue' --output text)

echo "Second web server is available at: $WEB_URL_2"
```

## 🧹 Step 11: CLI Implementation Cleanup

Finally, let's clean up all the resources we created:

```bash
# Delete the VPC-EC2 stack
echo "Deleting VPC-EC2 stack..."
aws cloudformation delete-stack --stack-name lab17-vpc-ec2-stack-cli

# Delete the S3 stack
echo "Deleting S3 stack..."
aws cloudformation delete-stack --stack-name lab17-s3-stack-cli

# Wait for the stacks to be deleted
echo "Waiting for stacks to be deleted (this may take several minutes)..."
aws cloudformation wait stack-delete-complete --stack-name lab17-vpc-ec2-stack-cli
aws cloudformation wait stack-delete-complete --stack-name lab17-s3-stack-cli

# Clean up local files
echo "Cleaning up local template files..."
cd ~
rm -rf ~/cloudformation-lab

echo "Cleanup complete!"
```

---

## 🧪 Optional Challenges

To deepen your understanding of CloudFormation, try these additional challenges:

1. **Nested Stacks**: Create a parent stack that references child stacks
2. **StackSets**: Deploy the same stack across multiple AWS regions
3. **Custom Resources**: Implement a custom resource using Lambda
4. **JSON Templates**: Convert one of the YAML templates to JSON format
5. **Drift Detection**: Modify a resource outside of CloudFormation and detect drift
6. **Condition Functions**: Add conditional resource creation to a template
7. **Dynamic References**: Use SecureString parameter in Parameter Store with dynamic references

---

## 🎓 Summary

In this lab, you've learned how to:
- Create CloudFormation templates in both YAML and JSON formats
- Deploy and manage stacks using both the AWS console and CLI
- Use parameters to make templates dynamic and reusable
- Create outputs to access important information about resources
- Use change sets to preview and apply changes
- Work with a variety of AWS resources defined in templates
- Validate templates before deployment
- Cleanup resources by deleting stacks

These skills are essential for adopting Infrastructure as Code practices in AWS, allowing you to provision resources consistently, maintain version control over your infrastructure, and automate deployment processes across different environments. 