# LAB01A: Working with EBS Volumes (AWS Console & CLI)

## 💾 Lab Overview

In this lab, you'll learn how to **create, attach, and manage EBS volumes** for your EC2 instances. You'll format and mount volumes, create snapshots for backups, and resize volumes as your storage needs change. This lab builds on your EC2 knowledge and introduces essential concepts for managing persistent data in AWS.

### 📚 Core Concepts for Beginners
- **What is EBS?** Amazon Elastic Block Store provides persistent block storage volumes for EC2 instances, similar to virtual hard drives
- **Block Storage vs. Object Storage**: EBS provides block-level storage (like a hard drive) rather than object storage (like S3)
- **Persistence**: EBS volumes persist independently from EC2 instances - you can terminate an instance but keep its data
- **Volume Types**: Different volume types (gp3, io2, st1, sc1) are optimized for different workloads and performance needs

### 🏗️ Real-World Analogy

Think of EBS volumes like external hard drives for your cloud servers:
- The **EC2 Instance** is like your computer
- **EBS Volumes** are like external hard drives you connect to it
- **Snapshots** are like backup copies of those drives that you store in a safe place
- **Volume Types** are like choosing between SSDs and HDDs based on speed requirements
- **Attachment/Detachment** is like plugging and unplugging drives from your computer

## 🌟 Why EBS?
EBS volumes are essential for scenarios where data needs to persist beyond the lifecycle of EC2 instances. They provide reliable, high-performance storage for applications, databases, file systems, and more.

### 💡 Real-World Use Cases
- Database storage for applications
- Content management systems
- Development and test environments
- Data processing applications
- Boot volumes for EC2 instances
- High-performance storage for enterprise applications

## 🎯 Objectives

By the end of this lab, you will be able to:

- Create different types of EBS volumes
- Attach and detach volumes from EC2 instances
- Format and mount volumes for use in Linux
- Create snapshots for backup
- Restore volumes from snapshots
- Modify volume size, type, and performance
- Understand EBS best practices for data management

## 🛠️ Prerequisites

- AWS account
- Completion of LAB01-EC2-Instance (you'll need a running EC2 instance)
- AWS CLI installed and configured (refer to the [CLI Setup Guide](../../CLI-Setup.md))
- SSH access to your EC2 instance

## 🌐 Architecture for This Lab

We'll build the following architecture:

```
                       ┌─────────────────┐
                       │                 │
                       │  EC2 Instance   │
                       │                 │
                       └─────────────────┘
                          ▲         ▲
                          │         │
                          │         │
         ┌────────────────┘         └────────────────┐
         │                                           │
         ▼                                           ▼
┌─────────────────┐                        ┌─────────────────┐
│   EBS Volume    │                        │   EBS Volume    │
│  (Console UI)   │                        │     (CLI)       │
└─────────────────┘                        └─────────────────┘
         │                                           │
         ▼                                           ▼
┌─────────────────┐                        ┌─────────────────┐
│    Snapshot     │                        │    Snapshot     │
│                 │                        │                 │
└─────────────────┘                        └─────────────────┘
```

## 📝 Part 1: Create and Attach EBS Volumes Using the AWS Console (UI)

### Step 1: Create a New EBS Volume

1. **Login to AWS Console**: 
   - Visit [https://console.aws.amazon.com](https://console.aws.amazon.com) and log in with your credentials
   - Make sure you're in the same region where your EC2 instance from LAB01 is running

2. **Navigate to EC2 Service**:
   - Type "EC2" in the search bar at the top of the console
   - Click on the "EC2" service that appears in the dropdown menu

3. **Navigate to Volumes**:
   - In the left sidebar, under "Elastic Block Store", click on "Volumes"
   - You should see at least one volume (the root volume of your EC2 instance)

4. **Create a New Volume**:
   - Click the "Create volume" button
   - Configure the volume:
     - **Volume Type**: General Purpose SSD (gp3)
     - **Size**: 10 GiB
     - **IOPS**: Leave at default (3000)
     - **Throughput**: Leave at default (125 MB/s)
     - **Availability Zone**: Select the SAME availability zone as your EC2 instance
       > ⚠️ **IMPORTANT:** Volumes can only be attached to instances in the same AZ
     - **Snapshot ID**: Don't select a snapshot (we're creating a new empty volume)
     - **Encryption**: Use default setting
   - Tags: Add a tag with key "Name" and value "lab01a-data-volume"
   - Click "Create volume"
   - You should see a success message and your new volume will appear in the volumes list

### Step 2: Attach the Volume to Your EC2 Instance

1. **Select Your New Volume**:
   - In the volumes list, select the volume you just created
   - Wait until the State shows "Available"

2. **Attach the Volume**:
   - With your volume selected, click on "Actions"
   - Select "Attach volume"
   - In the "Instance" field, start typing the name of your instance from LAB01 or select it from the dropdown
   - For "Device name", leave the default (likely /dev/sdf)
   - Click "Attach volume"
   - The volume state should change to "in-use"

### Step 3: Connect to Your EC2 Instance and Prepare the Volume

1. **Connect to Your EC2 Instance via SSH**:
   ```bash
   ssh -i path/to/your-key.pem ec2-user@your-instance-public-ip
   ```

2. **Verify the Volume is Attached**:
   ```bash
   lsblk
   ```
   - You should see your new volume listed (it might be named /dev/xvdf or similar)
   - Note that the volume doesn't have a mount point yet

3. **Create a File System on the Volume**:
   ```bash
   sudo mkfs -t xfs /dev/xvdf
   ```
   - Replace `/dev/xvdf` with your actual device name if different
   - This formats the volume with the XFS file system

4. **Create a Mount Point**:
   ```bash
   sudo mkdir /data
   ```

5. **Mount the Volume**:
   ```bash
   sudo mount /dev/xvdf /data
   ```

6. **Verify the Mount**:
   ```bash
   df -h
   ```
   - You should see your volume mounted at /data

7. **Test Writing Data**:
   ```bash
   sudo chown ec2-user:ec2-user /data
   echo "This is my EBS volume data" > /data/test.txt
   cat /data/test.txt
   ```

8. **Configure Auto-mounting on Reboot (Optional)**:
   ```bash
   sudo cp /etc/fstab /etc/fstab.backup
   sudo nano /etc/fstab
   ```
   - Add this line at the end:
   ```
   /dev/xvdf /data xfs defaults,nofail 0 2
   ```
   - Press CTRL+O to save, then CTRL+X to exit

## 📸 Part 2: Create a Snapshot Using the AWS Console (UI)

### Step 1: Create a Snapshot

1. **Navigate to Volumes**:
   - In the EC2 Dashboard, click on "Volumes" in the left sidebar
   - Select your data volume (the one with Name "lab01a-data-volume")

2. **Create a Snapshot**:
   - With the volume selected, click "Actions"
   - Select "Create snapshot"
   - Enter a description: "Backup of lab data volume"
   - Add a tag with key "Name" and value "lab01a-data-snapshot"
   - Click "Create snapshot"

3. **View the Snapshot**:
   - In the left sidebar, under "Elastic Block Store", click on "Snapshots"
   - You should see your new snapshot with status "pending"
   - Wait for the status to change to "completed"

## 🔄 Part 3: Resize an EBS Volume Using the AWS Console

### Step 1: Resize Using the Console

1. **Navigate to Volumes**:
   - In the EC2 Dashboard, click on "Volumes" in the left sidebar
   - Select your first data volume (with Name "lab01a-data-volume")

2. **Modify the Volume**:
   - With the volume selected, click "Actions"
   - Select "Modify volume"
   - Increase the size from 10 GiB to 15 GiB
   - Leave other settings unchanged
   - Click "Modify"
   - Click "Yes" to confirm
   - Wait for the modification to complete (check the "State" column)

3. **Extend the File System on Your EC2 Instance**:
   - Connect to your EC2 instance if not already connected
   - Check if the OS recognizes the new size:
     ```bash
     lsblk
     ```
   - Extend the file system to use the new space:
     ```bash
     sudo xfs_growfs /data
     ```
   - Verify the new size:
     ```bash
     df -h
     ```

## 💻 Part 4: Create and Attach EBS Volumes Using the AWS CLI

### Step 1: Create a New EBS Volume Using the CLI

1. **Get Your Availability Zone**:
   First, determine the availability zone of your EC2 instance:
   ```bash
   aws ec2 describe-instances --query "Reservations[*].Instances[*].{InstanceId:InstanceId,AZ:Placement.AvailabilityZone}" --output table
   ```

2. **Create the Volume**:
   ```bash
   aws ec2 create-volume \
     --volume-type gp3 \
     --size 8 \
     --availability-zone YOUR-AZ \
     --tag-specifications 'ResourceType=volume,Tags=[{Key=Name,Value=lab01a-cli-volume}]'
   ```
   - Replace `YOUR-AZ` with your actual availability zone (e.g., eu-west-1a)
   - Note the `VolumeId` in the output

3. **Verify the Volume Creation**:
   ```bash
   aws ec2 describe-volumes \
     --filters "Name=tag:Name,Values=lab01a-cli-volume" \
     --query "Volumes[*].{ID:VolumeId,State:State,Size:Size,Type:VolumeType}" \
     --output table
   ```

### Step 2: Attach the Volume to Your EC2 Instance Using the CLI

1. **Get Your Instance ID**:
   ```bash
   aws ec2 describe-instances \
     --filters "Name=instance-state-name,Values=running" \
     --query "Reservations[*].Instances[*].{ID:InstanceId,Name:Tags[?Key=='Name'].Value|[0]}" \
     --output table
   ```

2. **Attach the Volume**:
   ```bash
   aws ec2 attach-volume \
     --volume-id YOUR-VOLUME-ID \
     --instance-id YOUR-INSTANCE-ID \
     --device /dev/sdg
   ```
   - Replace `YOUR-VOLUME-ID` with the VolumeId from the previous step
   - Replace `YOUR-INSTANCE-ID` with your actual instance ID

3. **Verify the Attachment**:
   ```bash
   aws ec2 describe-volumes \
     --volume-ids YOUR-VOLUME-ID \
     --query "Volumes[*].{ID:VolumeId,State:State,AttachmentState:Attachments[0].State}" \
     --output table
   ```
   - The State should be "in-use" and AttachmentState should be "attached"

### Step 3: Connect to Your EC2 Instance and Prepare the Second Volume

1. **Connect to Your EC2 Instance via SSH** (if not already connected):
   ```bash
   ssh -i path/to/your-key.pem ec2-user@your-instance-public-ip
   ```

2. **Verify Both Volumes are Attached**:
   ```bash
   lsblk
   ```
   - You should now see both volumes (/dev/xvdf and /dev/xvdg)

3. **Format the New Volume**:
   ```bash
   sudo mkfs -t xfs /dev/xvdg
   ```

4. **Create a Mount Point**:
   ```bash
   sudo mkdir /data2
   ```

5. **Mount the Volume**:
   ```bash
   sudo mount /dev/xvdg /data2
   sudo chown ec2-user:ec2-user /data2
   ```

6. **Test the Second Volume**:
   ```bash
   echo "This is my CLI-created EBS volume" > /data2/cli-test.txt
   cat /data2/cli-test.txt
   ```

## 📸 Part 5: Create a Snapshot Using the AWS CLI

### Step 1: Create and Verify a Snapshot

1. **Create a Snapshot of Your CLI-created Volume**:
   ```bash
   aws ec2 create-snapshot \
     --volume-id YOUR-VOLUME-ID \
     --description "CLI created snapshot of lab volume" \
     --tag-specifications 'ResourceType=snapshot,Tags=[{Key=Name,Value=lab01a-cli-snapshot}]'
   ```
   - Replace `YOUR-VOLUME-ID` with the ID of your CLI-created volume

2. **Check Snapshot Status**:
   ```bash
   aws ec2 describe-snapshots \
     --filters "Name=tag:Name,Values=lab01a-cli-snapshot" \
     --query "Snapshots[*].{ID:SnapshotId,State:State,Progress:Progress}" \
     --output table
   ```
   - Initially, the State will be "pending" with a Progress percentage
   - You can re-run this command to see the progress update

## 🔄 Part 6: Resize an EBS Volume Using the AWS CLI

### Step 1: Resize Using the CLI

1. **Modify Your CLI-created Volume**:
   ```bash
   aws ec2 modify-volume \
     --volume-id YOUR-VOLUME-ID \
     --size 12
   ```
   - Replace `YOUR-VOLUME-ID` with the ID of your CLI-created volume
   - This increases the size to 12 GiB

2. **Check Modification Status**:
   ```bash
   aws ec2 describe-volumes-modifications \
     --volume-ids YOUR-VOLUME-ID \
     --query "VolumesModifications[*].{ID:VolumeId,OriginalSize:OriginalSize,TargetSize:TargetSize,Status:ModificationState}" \
     --output table
   ```

3. **Extend the File System on Your EC2 Instance**:
   - Wait until the modification state is "completed"
   - Connect to your EC2 instance if not already connected
   - Check if the OS recognizes the new size:
     ```bash
     lsblk
     ```
   - Extend the file system to use the new space:
     ```bash
     sudo xfs_growfs /data2
     ```
   - Verify the new size:
     ```bash
     df -h
     ```

## 🔁 Part 7: Create a Volume from a Snapshot Using the AWS CLI

### Step 1: Restore a Volume from a Snapshot

1. **Create a New Volume from the UI Snapshot**:
   ```bash
   aws ec2 describe-snapshots \
     --filters "Name=tag:Name,Values=lab01a-data-snapshot" \
     --query "Snapshots[*].{ID:SnapshotId,State:State}" \
     --output table
   ```
   - Note your snapshot ID, then:
   
   ```bash
   aws ec2 create-volume \
     --volume-type gp3 \
     --size 15 \
     --availability-zone YOUR-AZ \
     --snapshot-id YOUR-SNAPSHOT-ID \
     --tag-specifications 'ResourceType=volume,Tags=[{Key=Name,Value=lab01a-restored-volume}]'
   ```
   - Replace `YOUR-AZ` with your actual availability zone
   - Replace `YOUR-SNAPSHOT-ID` with the ID of your snapshot

2. **Verify That the Volume Was Created**:
   ```bash
   aws ec2 describe-volumes \
     --filters "Name=tag:Name,Values=lab01a-restored-volume" \
     --query "Volumes[*].{ID:VolumeId,State:State}" \
     --output table
   ```

## 🧪 Challenge Tasks (Optional)

1. **Multi-Volume Configuration**:
   - Create a third EBS volume
   - Attach it to your instance
   - Format it and mount it at /data3
   - Create a simple test file on it

2. **Striped Volume Configuration**:
   - Research and implement a software RAID 0 (striped) configuration using two new EBS volumes
   - Mount the RAID volume and test its performance

3. **Encrypted Volume**:
   - Create a new encrypted EBS volume
   - Attach it to your instance
   - Verify that you can use it just like an unencrypted volume

4. **Cross-Region Snapshot Copy**:
   - Create a snapshot of one of your volumes
   - Copy the snapshot to a different AWS region
   - Create a volume from the copied snapshot in the new region

## 🧹 Cleanup

To avoid incurring charges for resources you no longer need:

1. **Unmount Volumes**:
   ```bash
   sudo umount /data
   sudo umount /data2
   ```
   - Unmount any other volumes you've created

2. **Detach Volumes Using the Console**:
   - In the EC2 Dashboard, click on "Volumes"
   - Select a volume, click "Actions", and select "Detach volume"
   - Repeat for all non-root volumes

   Alternatively, use the CLI:
   ```bash
   aws ec2 detach-volume --volume-id YOUR-VOLUME-ID
   ```

3. **Delete Volumes**:
   - In the EC2 Dashboard, click on "Volumes"
   - Select a volume, click "Actions", and select "Delete volume"
   - Repeat for all non-root volumes you created in this lab

   Alternatively, use the CLI:
   ```bash
   aws ec2 delete-volume --volume-id YOUR-VOLUME-ID
   ```

4. **Delete Snapshots**:
   - In the EC2 Dashboard, click on "Snapshots"
   - Select your snapshots, click "Actions", and select "Delete snapshot"
   - Repeat for all snapshots created in this lab

   Alternatively, use the CLI:
   ```bash
   aws ec2 delete-snapshot --snapshot-id YOUR-SNAPSHOT-ID
   ```

## 🎯 Conclusion

Congratulations! You've successfully:
- Created and attached EBS volumes using both the AWS Console and CLI
- Formatted and mounted volumes on an EC2 instance
- Created snapshots for backups
- Restored volumes from snapshots
- Resized volumes to accommodate more data

These skills are critical for managing persistent storage in AWS environments. EBS volumes are the foundation of many AWS architectures that require reliable, persistent storage.

## 📚 Additional Resources

- [Amazon EBS Documentation](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AmazonEBS.html)
- [EBS Volume Types](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-volume-types.html)
- [Making an EBS Volume Available for Use](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-using-volumes.html)
- [EBS Snapshots](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSSnapshots.html)
- [EBS Encryption](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSEncryption.html) 