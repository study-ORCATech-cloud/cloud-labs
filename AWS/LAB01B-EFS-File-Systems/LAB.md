# LAB01B: Working with EFS File Systems (AWS Console & CLI)

## 📂 Lab Overview

In this lab, you'll set up **Amazon Elastic File System (EFS)** to create shared storage that can be accessed by multiple EC2 instances simultaneously. You'll configure mount targets in different availability zones, test shared file access, and learn how EFS provides elastic, highly available storage for your AWS workloads.

### 📚 Core Concepts for Beginners
- **What is EFS?** Amazon Elastic File System is a fully managed, scalable file storage service for use with EC2 instances
- **File Storage vs. Block Storage**: Unlike EBS (which is block storage attached to a single instance), EFS is file storage that can be accessed by multiple instances simultaneously
- **NFS Protocol**: EFS uses the Network File System protocol, which is standard for shared file systems
- **Elastic**: EFS automatically grows and shrinks as you add and remove files, with no need to provision storage in advance

### 🏗️ Real-World Analogy

Think of EFS like a network shared drive in a traditional office:
- **EFS** is like a central file server in your office building
- **Mount Targets** are like network ports that different departments can plug into
- **Security Groups** are like access badges that control who can enter which rooms
- **EC2 Instances** are like different computers in the office accessing the same shared files
- **Multi-AZ Design** is like having backup file servers in different buildings for disaster recovery

## 🌟 Why EFS?
EFS file systems are ideal for workloads and applications that require shared access to files. Unlike EBS volumes which can only be attached to a single EC2 instance at a time, EFS file systems support thousands of concurrent connections from multiple EC2 instances.

### 💡 Real-World Use Cases
- Content management systems
- Web servers with shared content
- Development environments
- Big data and analytics
- Media processing workflows
- Containerized applications with shared data
- Application logging and monitoring

## 🎯 Objectives

By the end of this lab, you will be able to:

- Create an EFS file system
- Configure mount targets in multiple availability zones
- Set up appropriate security groups for EFS access
- Mount EFS on Linux-based EC2 instances
- Configure shared access across multiple instances
- Understand EFS performance modes and throughput options
- Implement automatic mounting with /etc/fstab

## 🛠️ Prerequisites

- AWS account
- Completion of LAB01-EC2-Instance (you'll need a running EC2 instance)
- AWS CLI installed and configured (refer to the [CLI Setup Guide](../../CLI-Setup.md))
- SSH access to your EC2 instance

## 🌐 Architecture for This Lab

We'll build the following architecture:

```
                         ┌───────────┐
                         │           │
                         │    EFS    │
                         │           │
                         └─────┬─────┘
                               │
         ┌───────────────┬────┴────┬───────────────┐
         │               │         │               │
         ▼               ▼         ▼               ▼
┌─────────────────┐ ┌─────────┐ ┌─────────┐ ┌─────────────────┐
│  Mount Target   │ │   ...   │ │   ...   │ │  Mount Target   │
│ Availability    │ │         │ │         │ │ Availability    │
│    Zone 1       │ │         │ │         │ │    Zone N       │
└────────┬────────┘ └─────────┘ └─────────┘ └────────┬────────┘
         │                                           │
         ▼                                           ▼
┌─────────────────┐                        ┌─────────────────┐
│  EC2 Instance   │                        │  EC2 Instance   │
│      in AZ1     │                        │      in AZ2     │
└─────────────────┘                        └─────────────────┘
     │                                               │
     └───────────────────┬───────────────────────────┘
                         │
                         ▼
                  ┌─────────────────┐
                  │  Shared Files   │
                  │  Accessible by  │
                  │  All Instances  │
                  └─────────────────┘
```

## 📝 Part 1: Create an EFS File System Using the AWS Console (UI)

### Step 1: Create a New EFS File System

1. **Login to AWS Console**: 
   - Visit [https://console.aws.amazon.com](https://console.aws.amazon.com) and log in with your credentials
   - Make sure you're in the same region where your EC2 instance from LAB01 is running

2. **Navigate to EFS Service**:
   - Type "EFS" in the search bar at the top of the console
   - Click on the "EFS" service that appears in the dropdown menu

3. **Create a File System**:
   - Click the "Create file system" button
   - For a quick creation, you could use the "Create" button, but we'll customize settings:
   - Click "Customize" to access advanced settings

4. **Configure General Settings**:
   - **Name**: Enter `lab01b-efs`
   - **Automatic backups**: Uncheck (to avoid costs during this lab)
   - **Lifecycle management**: *Transition into Infrequent Access* Keep default (30 days since last access)
   - **Lifecycle management**: *Transition into Archive* change to `None`
   - **Encryption**: Keep "Enable encryption of data at rest" checked
   - **Throughput mode**: Select "Bursting"
     > 💡 **Info:** Bursting provides throughput that scales with file system size
   - **Performance mode**: Select "General Purpose"
     > ⚠️ **Note:** Max I/O mode is better for highly parallel workloads but has higher latency
   - Click "Next"

5. **Configure Network Access**:
   - Your default VPC should be selected
   - You'll see a list of all available subnets (one per availability zone)
   - For Mount targets, ensure all subnets are selected
   - For each subnet, you can:
     - Use the default security group, or
     - Create a new security group by clicking the "Create security group" link (recommended)
   - Click "Create security group" link
   - In the new tab that opens:
     - Name: `efs-access-sg`
     - Description: "Allow NFS traffic for EFS"
     - VPC: Select your default VPC
     - Add inbound rule:
       - Type: NFS (port 2049)
       - Source: Custom
       - Enter the security group ID of your EC2 instance from LAB01
       > 💡 **Best Practice:** This ensures only EC2 instances with that security group can access your EFS
     - Add tag: Key = "Name", Value = "efs-access-sg"
     - Click "Create security group"
   - Return to the EFS creation tab
   - Refresh the security group dropdown and select your new `efs-access-sg` for each subnet
   - Click "Next"

6. **Configure File System Policy**:
   - Select "Skip this step" for this lab
   - For production systems, you can use this to control access permissions

7. **Review and Create**:
   - Review your settings
   - Click "Create" to create your EFS file system
   - Wait for the file system status to become "Available"

### Step 2: Update EC2 Security Group

1. **Navigate to EC2 Service**:
   - Type "EC2" in the search bar at the top of the console
   - Click on the "EC2" service that appears in the dropdown menu

2. **Navigate to Security Groups**:
   - In the left sidebar, under "Network & Security", click on "Security Groups"
   - Find and select the security group that's attached to your EC2 instance
   
3. **Add Outbound Rule for EFS**:
   - With your security group selected, click on the "Outbound rules" tab
   - Click "Edit outbound rules"
   - Click "Add rule"
   - For the new rule:
     - Type: NFS (port 2049)
     - Destination: Custom
     - Enter the ID of your EFS security group (`efs-access-sg`)
     - Description: "Allow outbound to EFS"
   - Click "Save rules"

### Step 3: Mount the EFS File System on Your EC2 Instance

1. **Connect to Your EC2 Instance via SSH**:
   ```bash
   ssh -i path/to/your-key.pem ec2-user@your-instance-public-ip
   ```

2. **Install the NFS Client** (if not already installed):
   ```bash
   sudo yum install -y nfs-utils
   ```

3. **Create a Mount Point**:
   ```bash
   sudo mkdir /efs
   ```

4. **Get Your EFS File System ID and DNS Name**:
   - Return to the EFS console
   - Click on your file system (lab01b-efs)
   - Note the File System ID (fs-xxxxxxxx)
   - In the "Network" tab, find the DNS name for your mount targets (it will look something like: fs-xxxxxxxx.efs.eu-west-1.amazonaws.com)

5. **Mount the File System**:
   ```bash
   sudo mount -t nfs4 -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport fs-xxxxxxxx.efs.eu-west-1.amazonaws.com:/ /efs
   ```
   - Replace `fs-xxxxxxxx.efs.eu-west-1.amazonaws.com` with your actual EFS DNS name

6. **Verify the Mount**:
   ```bash
   df -h
   ```
   - You should see your EFS file system mounted at /efs

7. **Set Permissions and Create a Test File**:
   ```bash
   sudo chown ec2-user:ec2-user /efs
   echo "This is my EFS test file from instance 1" > /efs/test-file.txt
   cat /efs/test-file.txt
   ```

## 🖥️ Part 2: Launch a Second EC2 Instance and Test Shared Access

### Step 1: Launch Another EC2 Instance in a Different AZ

1. **Navigate to EC2 Dashboard**:
   - Go back to the EC2 Dashboard
   - Click "Launch instance"

2. **Configure the Instance**:
   - Name: `lab01b-instance2`
   - AMI: Amazon Linux 2 AMI (same as LAB01)
   - Instance type: t2.micro
   - Key pair: Use the same key pair as your first instance
   - Network: Same VPC as your first instance
   - **Subnet**: Select a DIFFERENT Availability Zone than your first instance
   - **Security group**: Select the SAME security group as your first instance
     > 💡 **Info:** This ensures this instance can also access the EFS file system
   - Leave other settings at default
   - Click "Launch instance"

3. **Connect to the New Instance**:
   - Wait for the instance to be in the "Running" state and pass its status checks
   - Connect via SSH using its public IP address:
   ```bash
   ssh -i path/to/your-key.pem ec2-user@second-instance-public-ip
   ```

4. **Install NFS Client and Mount EFS**:
   ```bash
   sudo yum install -y nfs-utils
   sudo mkdir /efs
   sudo mount -t nfs4 -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport fs-xxxxxxxx.efs.eu-west-1.amazonaws.com:/ /efs
   ```
   - Use the same EFS DNS name as before

5. **Verify Shared Access**:
   ```bash
   ls -la /efs
   cat /efs/test-file.txt
   ```
   - You should see the file you created from the first instance
   
6. **Create a New File from the Second Instance**:
   ```bash
   sudo chown ec2-user:ec2-user /efs
   echo "This is my EFS test file from instance 2" > /efs/test-file2.txt
   ```

7. **Verify on the First Instance**:
   - Return to your SSH session for the first instance
   - Run:
   ```bash
   ls -la /efs
   cat /efs/test-file2.txt
   ```
   - You should see the file created from the second instance

## 💻 Part 3: Create an EFS File System Using the AWS CLI

### Step 1: Create a New EFS File System Using the CLI

1. **From your local machine (not the EC2 instance), create a new EFS file system**:
   ```bash
   aws efs create-file-system \
     --performance-mode generalPurpose \
     --throughput-mode bursting \
     --encrypted \
     --tags Key=Name,Value=lab01b-cli-efs \
     --region eu-west-1
   ```
   - Replace `eu-west-1` with your actual region if different
   - Note the FileSystemId in the output

2. **Create a Security Group for the CLI-created EFS**:
   ```bash
   VPC_ID=$(aws ec2 describe-vpcs --query "Vpcs[0].VpcId" --output text)
   
   aws ec2 create-security-group \
     --group-name efs-cli-sg \
     --description "Security group for CLI-created EFS" \
     --vpc-id $VPC_ID
   ```
   - Note the GroupId in the output (sg-xxxxxxxx)

3. **Add an Inbound Rule to the Security Group**:
   ```bash
   EC2_SG_ID=$(aws ec2 describe-security-groups \
     --filters "Name=group-name,Values=*lab01*" \
     --query "SecurityGroups[0].GroupId" \
     --output text)
   
   aws ec2 authorize-security-group-ingress \
     --group-id sg-xxxxxxxx \
     --protocol tcp \
     --port 2049 \
     --source-group $EC2_SG_ID
   ```
   - Replace `sg-xxxxxxxx` with your actual security group ID from the previous step

4. **Add an Outbound Rule to Your EC2 Security Group**:
   ```bash
   aws ec2 authorize-security-group-egress \
     --group-id $EC2_SG_ID \
     --protocol tcp \
     --port 2049 \
     --source-group sg-xxxxxxxx
   ```
   - Replace `sg-xxxxxxxx` with your EFS security group ID

5. **Get Subnet IDs for Mount Targets**:
   ```bash
   aws ec2 describe-subnets \
     --query "Subnets[*].{SubnetId:SubnetId,AvailabilityZone:AvailabilityZone,CidrBlock:CidrBlock}" \
     --output table
   ```
   - Note the subnet IDs you want to use for mount targets

6. **Create Mount Targets in Each Subnet**:
   ```bash
   # For the first subnet
   aws efs create-mount-target \
     --file-system-id fs-xxxxxxxx \
     --subnet-id subnet-xxxxxxxx \
     --security-groups sg-xxxxxxxx
   
   # For the second subnet
   aws efs create-mount-target \
     --file-system-id fs-xxxxxxxx \
     --subnet-id subnet-yyyyyyyy \
     --security-groups sg-xxxxxxxx
   ```
   - Replace `fs-xxxxxxxx` with your actual file system ID
   - Replace `subnet-xxxxxxxx` and `subnet-yyyyyyyy` with your actual subnet IDs
   - Replace `sg-xxxxxxxx` with your actual EFS security group ID
   - Repeat for any additional subnets you want to use

7. **Verify the Mount Targets**:
   ```bash
   aws efs describe-mount-targets \
     --file-system-id fs-xxxxxxxx
   ```
   - Replace `fs-xxxxxxxx` with your actual file system ID
   - Wait until the LifeCycleState is "available" for all mount targets

### Step 2: Connect to Your EC2 Instances and Mount the New EFS

1. **Get the DNS Name for Your CLI-created EFS**:
   ```bash
   aws efs describe-file-systems \
     --file-system-id fs-xxxxxxxx \
     --query "FileSystems[0].FileSystemId" \
     --output text
   ```
   - Replace `fs-xxxxxxxx` with your actual file system ID
   - The DNS name follows the format: `fs-xxxxxxxx.efs.eu-west-1.amazonaws.com`

2. **Connect to Your First EC2 Instance**:
   ```bash
   ssh -i path/to/your-key.pem ec2-user@your-instance-public-ip
   ```

3. **Create a Mount Point and Mount the New EFS**:
   ```bash
   sudo mkdir /efs-cli
   sudo mount -t nfs4 -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport fs-xxxxxxxx.efs.eu-west-1.amazonaws.com:/ /efs-cli
   ```
   - Replace `fs-xxxxxxxx.efs.eu-west-1.amazonaws.com` with your actual EFS DNS name

4. **Create a Test File**:
   ```bash
   sudo chown ec2-user:ec2-user /efs-cli
   echo "This is my CLI-created EFS file system - from instance 1" > /efs-cli/cli-test.txt
   ```

5. **Connect to Your Second EC2 Instance and Do the Same**:
   ```bash
   ssh -i path/to/your-key.pem ec2-user@second-instance-public-ip
   sudo mkdir /efs-cli
   sudo mount -t nfs4 -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport fs-xxxxxxxx.efs.eu-west-1.amazonaws.com:/ /efs-cli
   cat /efs-cli/cli-test.txt
   ```
   - You should see the file created from the first instance

## ⚙️ Part 4: Configure Automatic Mounting with /etc/fstab

### Step 1: Set Up Persistent Mounts

1. **Connect to Your First EC2 Instance**:
   ```bash
   ssh -i path/to/your-key.pem ec2-user@your-instance-public-ip
   ```

2. **Install the Amazon EFS Mount Helper**:
   ```bash
   sudo yum install -y amazon-efs-utils
   ```

3. **Backup Your fstab File**:
   ```bash
   sudo cp /etc/fstab /etc/fstab.backup
   ```

4. **Add EFS to fstab for Both File Systems**:
   ```bash
   # Get your file system IDs and replace in the following commands
   echo "fs-xxxxxxxx.efs.eu-west-1.amazonaws.com:/ /efs efs defaults,_netdev 0 0" | sudo tee -a /etc/fstab
   echo "fs-yyyyyyyy.efs.eu-west-1.amazonaws.com:/ /efs-cli efs defaults,_netdev 0 0" | sudo tee -a /etc/fstab
   ```
   - Replace `fs-xxxxxxxx` and `fs-yyyyyyyy` with your actual file system IDs

5. **Test the Configuration**:
   ```bash
   sudo umount /efs
   sudo umount /efs-cli
   sudo mount -a
   ```

6. **Verify the Mounts**:
   ```bash
   df -h
   ```
   - You should see both EFS file systems mounted

7. **Do the Same on Your Second EC2 Instance**:
   - Connect to your second instance
   - Install EFS utils and update fstab
   - Test with unmount and mount -a

## 🧪 Challenge Tasks (Optional)

1. **EFS Access Points**:
   - Create an EFS Access Point from the console
   - Configure user, group, and path settings
   - Mount the file system using the Access Point
   - Test file permissions

2. **EFS Lifecycle Management**:
   - Experiment with changing lifecycle policies
   - Monitor file placement in storage tiers

3. **Encryption in Transit**:
   - Configure an EFS mount with encryption in transit
   - The mount command will need the tls option

4. **Different Performance Modes**:
   - Create another EFS file system with Max I/O mode
   - Compare performance for parallel access patterns

## 🧹 Cleanup

To avoid incurring charges for resources you no longer need:

1. **Unmount File Systems**:
   ```bash
   sudo umount /efs
   sudo umount /efs-cli
   ```
   - Run these commands on both EC2 instances

2. **Delete Mount Targets for Both EFS File Systems**:
   From your local machine:
   ```bash
   # For the UI-created EFS
   aws efs describe-mount-targets --file-system-id fs-xxxxxxxx --query "MountTargets[*].MountTargetId" --output text
   
   # For each mount target ID
   aws efs delete-mount-target --mount-target-id fsmt-xxxxxxxx
   
   # For the CLI-created EFS
   aws efs describe-mount-targets --file-system-id fs-yyyyyyyy --query "MountTargets[*].MountTargetId" --output text
   
   # For each mount target ID
   aws efs delete-mount-target --mount-target-id fsmt-yyyyyyyy
   ```
   - Replace `fs-xxxxxxxx` and `fs-yyyyyyyy` with your actual file system IDs
   - Replace `fsmt-xxxxxxxx` and `fsmt-yyyyyyyy` with your actual mount target IDs

3. **Wait for Mount Target Deletion**:
   ```bash
   aws efs describe-mount-targets --file-system-id fs-xxxxxxxx
   ```
   - Repeat until no mount targets are returned
   - Do the same for the CLI-created EFS

4. **Delete EFS File Systems**:
   ```bash
   aws efs delete-file-system --file-system-id fs-xxxxxxxx
   aws efs delete-file-system --file-system-id fs-yyyyyyyy
   ```
   - Replace with your actual file system IDs

5. **Delete Security Groups**:
   ```bash
   aws ec2 delete-security-group --group-id sg-xxxxxxxx
   ```
   - Replace with the ID of the EFS-specific security groups you created
   - You may need to remove the references from your EC2 security group's outbound rules first

6. **Terminate the Second EC2 Instance**:
   ```bash
   aws ec2 terminate-instances --instance-ids i-xxxxxxxx
   ```
   - Replace with your actual instance ID for the second instance
   - Alternatively, use the AWS Console

## 🎯 Conclusion

Congratulations! You've successfully:
- Created EFS file systems using both the AWS Console and CLI
- Set up mount targets in multiple availability zones
- Configured appropriate security groups for access
- Mounted EFS on EC2 instances in different AZs
- Demonstrated shared file access
- Set up automatic mounting with /etc/fstab

These skills are essential for designing applications that require shared file storage with high availability. EFS is perfect for web content management, development environments, containerized applications, and any scenario where multiple instances need to access the same files.

## 📚 Additional Resources

- [Amazon EFS Documentation](https://docs.aws.amazon.com/efs/latest/ug/whatisefs.html)
- [EFS Performance](https://docs.aws.amazon.com/efs/latest/ug/performance.html)
- [Mounting EFS File Systems](https://docs.aws.amazon.com/efs/latest/ug/mounting-fs.html)
- [EFS Access Points](https://docs.aws.amazon.com/efs/latest/ug/efs-access-points.html)
- [EFS Lifecycle Management](https://docs.aws.amazon.com/efs/latest/ug/lifecycle-management-efs.html) 
