# LAB01: Launch an EC2 Instance (AWS Console & CLI)

## 🧠 Lab Overview

Welcome to your first AWS hands-on lab! In this exercise, you will learn how to create a virtual server (EC2 instance) on AWS. You will perform the same operation using both the **AWS Management Console (UI)** and the **AWS Command Line Interface (CLI)**. This dual approach ensures you get comfortable with both manual and script-based cloud operations.

### 📚 Core Concepts for Beginners
- **What is EC2?** EC2 stands for "Elastic Compute Cloud" - think of it as a computer in the cloud that you can rent by the hour
- **Virtual Machine vs. Physical Server**: Unlike physical servers that require hardware setup, virtual machines can be created in seconds through software
- **Cloud Computing Benefits**: Flexibility to scale up/down, pay only for what you use, and access from anywhere

## 🌟 Why EC2?
Amazon EC2 (Elastic Compute Cloud) is a foundational AWS service that lets you rent virtual servers on demand. It's commonly used to host websites, run applications, or perform general-purpose computing tasks.

### 💡 Real-World Use Cases
- Hosting websites and web applications
- Running backend services for mobile apps
- Data processing and batch jobs
- Development and testing environments
- Running business applications like CRM or ERP systems

---

## 🎯 Objectives

By the end of this lab, you will be able to:

- Understand what EC2 is and why it's important
- Launch an EC2 instance using the AWS Console
- Launch an EC2 instance using the AWS CLI
- Understand key EC2 configuration components (AMI, instance type, key pair, security group)
- Connect to the instance using SSH
- Clean up by terminating the instance

---

## 🛠️ Prerequisites

- AWS account
- AWS CLI installed and configured (refer to the [CLI Setup Guide](../../CLI-Setup.md))
- A key pair for SSH access (you can create one during the lab)
- Basic terminal/SSH knowledge recommended

---

## 📝 Part 1: Launch EC2 Using the AWS Console (UI)

1. **Login to AWS Console**: 
   - Visit [https://console.aws.amazon.com](https://console.aws.amazon.com) and log in with your credentials
   - If this is your first time, you may see a welcome screen - you can skip the tour

2. **Navigate to EC2 Service**:
   - Type "EC2" in the search bar at the top of the console
   - Click on the "EC2" service that appears in the dropdown menu
   - Alternatively, click on "Services" in the top bar, then select "EC2" under the Compute section
   - You should now see the EC2 Dashboard

3. **Launch an Instance**:
   - In the EC2 Dashboard, look for a prominent orange button labeled "Launch Instance"
   - Click on the dropdown arrow next to it and select "Launch Instance"
   - This will take you to the instance creation wizard

4. **Configure Your Instance Step-by-Step**:
   
   a. **Name and Tag Your Instance**:
   - Enter `lab01-ui-instance` in the "Name" field
   - This name helps you identify your instance in the AWS Console

   b. **Choose an Amazon Machine Image (AMI)**:
   - Under "Application and OS Images", find "Amazon Linux 2 AMI (HVM)"
   - Look for "Free tier eligible" tag to avoid charges
   - The AMI contains the operating system and initial software for your server
   
   c. **Select Instance Type**:
   - Under "Instance Type", select `t2.micro`
   - This is covered by the AWS Free Tier and provides 1 virtual CPU and 1 GB of memory
   - Note: If you don't see t2.micro, it might be called "t3.micro" or similar in your region - any "Free tier eligible" option is fine

   d. **Create or Select a Key Pair**:
   - Under "Key pair (login)", click "Create new key pair"
   - Key pair name: `lab01-key`
   - Key pair type: RSA
   - Private key file format: .pem (for Mac/Linux) or .ppk (for Windows with PuTTY)
   - Click "Create key pair"
   - The key will automatically download to your computer - **SAVE THIS FILE SECURELY!**
   - This key is the only way to access your instance; if lost, you won't be able to connect

   e. **Configure Network Settings**:
   - Click "Edit" under "Network settings"
   - VPC: Leave as default
   - Subnet: Leave as default (or choose any "Public subnet")
   - Auto-assign public IP: Enable
   - Create security group: Yes
   - Security group name: `lab01-sg`
   - Description: "Security group for Lab 01 EC2 instance"
   
   f. **Configure Security Group Rules**:
   - You should see one rule already added for SSH (port 22)
   - Change "Source type" to "My IP" (this restricts access to only your computer's IP address)
   - Description: "SSH access from my IP"
   - This restricts remote access to only your computer for security reasons

   g. **Configure Storage**:
   - Leave the default storage settings (typically 8 GB)
   - This is the virtual hard drive for your server

   h. **Advanced Details (Optional)**:
   - You can leave all advanced settings at their default values for this lab

5. **Review and Launch**:
   - Scroll down and review all your settings
   - Check for any warnings or errors
   - Click the "Launch instance" button at the bottom right
   - You'll see a confirmation page stating "Successfully initiated launch of instance"
   - Click on the instance ID link to go directly to your instance details

6. **Wait for the Instance to Start**:
   - In the Instances list, watch your instance's "Instance state" column
   - Initially, it will show "Pending" while AWS provisions your virtual server
   - After 1-2 minutes, it should change to "Running"
   - Wait until "Status check" shows "2/2 checks passed" (this might take a few more minutes)

7. **Access Instance Details**:
   - With your instance selected, look at the "Details" tab at the bottom
   - Find and note down the "Public IPv4 address" (e.g., 18.234.123.45)
   - This is the public address of your server on the internet

8. **Connect to Your Instance via SSH**:
   
   **For Mac/Linux Users**:
   - Open Terminal
   - Change permissions on your key file:
     ```bash
     chmod 400 ~/Downloads/lab01-key.pem
     ```
     (Adjust the path if your key is located elsewhere)
   - Connect with SSH:
     ```bash
     ssh -i ~/Downloads/lab01-key.pem ec2-user@YOUR_INSTANCE_PUBLIC_IP
     ```
     Replace YOUR_INSTANCE_PUBLIC_IP with the actual IP from step 7
   - If prompted with "Are you sure you want to continue connecting", type "yes"

   **For Windows Users**:
   - If using Windows PowerShell:
     ```
     ssh -i C:\path\to\lab01-key.pem ec2-user@YOUR_INSTANCE_PUBLIC_IP
     ```
   - If using PuTTY:
     1. Open PuTTY
     2. In the Host Name field, enter: `ec2-user@YOUR_INSTANCE_PUBLIC_IP`
     3. In the left navigation, go to Connection > SSH > Auth > Credentials
     4. Click "Browse" and select your .ppk file
     5. Click "Open" to connect

   **Troubleshooting Connection Issues**:
   - If the connection times out, check:
     1. Is your instance "running" and passed status checks?
     2. Did you configure the security group to allow SSH from your IP?
     3. Are you using the correct key file and username?
   - If you see "Permission denied", check file permissions on your key (chmod 400)

9. **Explore Your Linux Server**:
   - Once connected, you'll see a welcome message and a command prompt
   - You are now connected to your EC2 instance!
   - Try these commands to explore:

   a. **Check system information**:
   ```bash
   uname -a                     # Display Linux system information
   cat /etc/os-release          # Check OS version details
   ```

   b. **Monitor system resources**:
   ```bash
   top                          # Show running processes and resource usage
   ```
   (Press 'q' to exit top)

   c. **Check network connectivity**:
   ```bash
   ping -c 4 amazon.com         # Test internet connectivity
   curl https://example.com     # Fetch a web page
   ```

   d. **View available disk space**:
   ```bash
   df -h                        # Show disk space usage
   ```

   e. **Create a file**:
   ```bash
   echo "Hello from my EC2 instance" > hello.txt
   cat hello.txt
   ```

10. **Exit the SSH Session**:
    ```bash
    exit
    ```

Congratulations! You've successfully launched and connected to an EC2 instance using the AWS Console!

---

## 💻 Part 2: Launch EC2 Using AWS CLI

Now let's create another EC2 instance, but this time using command-line tools instead of the graphical interface.

### 📋 Understanding AWS CLI

The AWS CLI allows you to control AWS services directly from your terminal. This is powerful for:
- Automation and scripting
- Faster operations once you're familiar with commands
- Reproducible infrastructure setups

### 🖥️ CLI Steps

1. **Open Your Terminal or Command Prompt**:
   - On Windows: Open PowerShell or Command Prompt
   - On Mac/Linux: Open Terminal

2. **Verify AWS CLI Installation**:
   ```bash
   aws --version
   ```
   You should see version information confirming the AWS CLI is installed

3. **Create a Security Group**:
   ```bash
   aws ec2 create-security-group \
     --group-name lab01-cli-sg \
     --description "Security group for CLI lab"
   ```
   
   You should see output like:
   ```json
   {
       "GroupId": "sg-0123456789abcdef0"
   }
   ```
   
   Make note of this GroupId for future reference

4. **Allow SSH Access to Your Security Group**:

   a. **First, find your public IP address**:
   - Visit [https://whatismyipaddress.com](https://whatismyipaddress.com) or google "what is my ip"
   - Copy your IPv4 address (e.g., 203.0.113.0)

   b. **Add an inbound rule for SSH**:
   ```bash
   aws ec2 authorize-security-group-ingress \
     --group-name lab01-cli-sg \
     --protocol tcp \
     --port 22 \
     --cidr YOUR_PUBLIC_IP/32
   ```
   
   Replace `YOUR_PUBLIC_IP` with your actual IP address, keeping the `/32` at the end
   
   Example: `--cidr 203.0.113.0/32`
   
   The `/32` means "this exact IP only" for security purposes

5. **Get the Latest Amazon Linux 2 AMI ID**:

   Different regions have different AMI IDs. This command finds the right one for your region:
   
   ```bash
   aws ssm get-parameters-by-path \
     --path "/aws/service/ami-amazon-linux-latest" \
     --query "Parameters[?Name=='/aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-gp2'].Value" \
     --output text
   ```
   
   You'll receive an AMI ID like: `ami-0123456789abcdef0`
   
   Save this ID for the next step

6. **List Available Key Pairs**:

   Let's see what key pairs are available in your account:
   
   ```bash
   aws ec2 describe-key-pairs
   ```
   
   You should see the `lab01-key` we created earlier. If not, you'll need to create a new key pair:
   
   ```bash
   aws ec2 create-key-pair \
     --key-name lab01-cli-key \
     --query "KeyMaterial" \
     --output text > lab01-cli-key.pem
   ```
   
   On Mac/Linux, make the key file private:
   ```bash
   chmod 400 lab01-cli-key.pem
   ```

7. **Launch an EC2 Instance**:

   Now we'll use all the pieces we've gathered to launch an instance:
   
   ```bash
   aws ec2 run-instances \
     --image-id ami-0123456789abcdef0 \
     --count 1 \
     --instance-type t2.micro \
     --key-name lab01-key \
     --security-groups lab01-cli-sg \
     --tag-specifications 'ResourceType=instance,Tags=[{Key=Name,Value=lab01-cli-instance}]'
   ```
   
   Replace:
   - `ami-0123456789abcdef0` with the AMI ID from step 5
   - `lab01-key` with your key pair name from step 6 (if different)
   
   You'll get a large JSON response with details about your new instance, including:
   - InstanceId (e.g., `i-0123456789abcdef0`)
   - State (should be `pending`)
   - Private and Public IP addresses (if assigned)
   
   Save the InstanceId for later use

8. **Wait for the Instance to Start Running**:

   Check the status of your instance:
   
   ```bash
   aws ec2 describe-instances \
     --instance-ids i-0123456789abcdef0 \
     --query "Reservations[*].Instances[*].State.Name" \
     --output text
   ```
   
   Replace `i-0123456789abcdef0` with your actual instance ID
   
   Wait until you see "running" as the output

9. **Get Your Instance's Public IP Address**:

   ```bash
   aws ec2 describe-instances \
     --instance-ids i-0123456789abcdef0 \
     --query "Reservations[*].Instances[*].PublicIpAddress" \
     --output text
   ```
   
   Replace `i-0123456789abcdef0` with your actual instance ID
   
   This command will output just the public IP address of your instance

10. **Connect to Your Instance via SSH**:

    ```bash
    ssh -i path/to/your-key.pem ec2-user@PUBLIC_IP
    ```
    
    Replace:
    - `path/to/your-key.pem` with the actual path to your key file
    - `PUBLIC_IP` with the IP address from step 9
    
    For example:
    ```bash
    ssh -i ~/Downloads/lab01-key.pem ec2-user@18.234.123.45
    ```

11. **Explore Your Instance**:

    Just like with the UI-created instance, try some Linux commands:
    
    ```bash
    # System info
    hostname
    whoami
    
    # Check available memory
    free -h
    
    # Check disk space
    df -h
    
    # View network interfaces
    ip addr show
    
    # Check running processes
    ps aux
    ```

12. **Exit SSH Connection**:
    ```bash
    exit
    ```

Congratulations! You've now created an EC2 instance using the AWS CLI!

---

## 🧪 Optional Challenge: Host a Simple Website

Let's turn your EC2 instance into a web server and host a simple HTML page. This demonstrates a common real-world use case for EC2.

1. **SSH Into Your Instance**:
   ```bash
   ssh -i path/to/your-key.pem ec2-user@YOUR_PUBLIC_IP
   ```

2. **Install the Apache Web Server**:
   ```bash
   sudo yum update -y                   # Update all packages
   sudo yum install -y httpd            # Install Apache
   sudo systemctl start httpd           # Start the web server
   sudo systemctl enable httpd          # Configure to start on boot
   ```

3. **Check Apache Status**:
   ```bash
   sudo systemctl status httpd
   ```
   You should see "active (running)" in the output

4. **Create a Simple Web Page**:
   ```bash
   echo "<html>
   <head><title>My EC2 Web Server</title></head>
   <body>
     <h1>Hello from EC2!</h1>
     <p>This page is being served from my Amazon EC2 instance.</p>
     <p>Current server time: $(date)</p>
   </body>
   </html>" | sudo tee /var/www/html/index.html
   ```

5. **Allow HTTP Traffic to Your Instance**:

   **For the Console-created instance**:
   - Go back to the AWS Console
   - Select your instance
   - Select the "Security" tab
   - Click on the Security Group link
   - Click "Edit inbound rules"
   - Click "Add rule"
   - Type: HTTP, Source: Anywhere-IPv4
   - Click "Save rules"

   **For the CLI-created instance**:
   
   From your local terminal (not the SSH session):
   ```bash
   aws ec2 authorize-security-group-ingress \
     --group-name lab01-cli-sg \
     --protocol tcp \
     --port 80 \
     --cidr 0.0.0.0/0
   ```
   This allows web traffic (HTTP) from any IP address to reach your server

6. **Test Your Website**:
   - Open a web browser
   - Navigate to `http://YOUR_PUBLIC_IP` (not https)
   - You should see your "Hello from EC2!" web page

7. **Customize Your Web Page (Optional)**:
   ```bash
   sudo nano /var/www/html/index.html
   ```
   Use the nano text editor to make changes, then press CTRL+O to save and CTRL+X to exit

---

## 🔍 Troubleshooting Common Issues

### Can't Connect to EC2

1. **Instance Not Running**:
   ```bash
   aws ec2 describe-instances \
     --instance-ids YOUR_INSTANCE_ID \
     --query "Reservations[*].Instances[*].State.Name" \
     --output text
   ```
   Ensure it shows "running"

2. **Security Group Issues**:
   ```bash
   aws ec2 describe-security-groups \
     --group-names lab01-cli-sg
   ```
   Check that port 22 is open to your IP address

3. **Key Pair Problems**:
   - Ensure you're using the correct key
   - Ensure proper permissions: `chmod 400 your-key.pem`
   - Try specifying the username explicitly: `ec2-user@YOUR_IP`

### Website Not Loading

1. **Apache Not Running**:
   ```bash
   sudo systemctl status httpd
   ```
   If not running: `sudo systemctl start httpd`

2. **Port 80 Not Open**:
   Check security group settings for HTTP (port 80) access

3. **Firewall Issues**:
   ```bash
   sudo systemctl status firewalld
   ```
   If active, allow HTTP: `sudo firewall-cmd --permanent --add-service=http`

---

## 🌀 Cleanup - IMPORTANT!

To avoid ongoing charges, let's terminate both instances:

1. **Find Your Instance IDs**:
   ```bash
   aws ec2 describe-instances \
     --filters "Name=tag:Name,Values=lab01-ui-instance,lab01-cli-instance" \
     --query "Reservations[*].Instances[*].InstanceId" \
     --output text
   ```

2. **Terminate the Instances**:
   ```bash
   aws ec2 terminate-instances --instance-ids i-0123456789abcdef0 i-0abcdef1234567890
   ```
   Replace with your actual instance IDs

3. **Verify Termination**:
   ```bash
   aws ec2 describe-instances \
     --instance-ids YOUR_INSTANCE_ID \
     --query "Reservations[*].Instances[*].State.Name" \
     --output text
   ```
   Wait until you see "terminated"

4. **Delete Security Groups** (Once instances are terminated):
   ```bash
   aws ec2 delete-security-group --group-name lab01-cli-sg
   ```
   Repeat for any other security groups you created

5. **Check AWS Console**:
   - Go to EC2 Dashboard
   - Verify no instances are running
   - Check under Security Groups to ensure your lab groups are deleted

---

## ✅ What You've Learned

Congratulations! In this lab, you have:

1. Created virtual servers in the cloud using both the AWS Console and CLI
2. Learned about key EC2 components:
   - Amazon Machine Images (AMIs)
   - Instance types and sizing
   - Security groups and network access
   - SSH key pairs and secure access
3. Connected to and managed Linux servers remotely
4. Configured a simple web server
5. Properly cleaned up resources to avoid charges

These skills are foundational for cloud computing and will be built upon in future labs. You now understand how to deploy and manage virtual machines in the AWS cloud!

---

## 📚 Additional Resources

- [EC2 User Guide](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/concepts.html)
- [AWS CLI EC2 Command Reference](https://docs.aws.amazon.com/cli/latest/reference/ec2/index.html)
- [Linux Administration Basics](https://www.tecmint.com/linux-commands-for-beginners/)
- [AWS Free Tier Usage Tracking](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/tracking-free-tier-usage.html)

---

Happy Clouding ☁️!

