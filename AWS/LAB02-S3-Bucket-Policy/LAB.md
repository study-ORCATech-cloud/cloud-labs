# LAB02: Create and Manage S3 Buckets with Bucket Policies

## 📄 Lab Overview

Welcome to Lab 02! In this hands-on lab, you'll learn how to create and manage Amazon S3 (Simple Storage Service) buckets using both the **AWS Management Console** and the **AWS CLI**. You'll also learn about bucket policies that control access to your S3 resources. This lab introduces you to object storage in AWS and helps you understand use cases for static content hosting, data archiving, and backups.

### 📚 Core Concepts for Beginners

- **What is S3?** Amazon S3 (Simple Storage Service) is cloud storage for files and data of any type and size.
- **Object Storage vs. File Storage**: S3 is object storage, meaning you store complete files instead of blocks of data.
- **Bucket vs. Object**: A bucket is like a folder that holds objects (files); all buckets must have globally unique names.
- **Bucket Policy**: A JSON document that defines who can access your bucket and what actions they can perform.

## 🔎 What You'll Learn

- What Amazon S3 is and how it works
- How to create an S3 bucket using the AWS Console and CLI
- How to upload, list, and delete files in a bucket
- How to configure bucket policies for access control
- How to set up static website hosting

## 🛠️ Prerequisites

- AWS account
- AWS CLI installed and configured (refer to the [CLI Setup Guide](../../CLI-Setup.md))
- Basic understanding of file systems

## 🏠 Part 1: Create an S3 Bucket (Console)

### Step 1: Navigate to S3 Service
1. Log in to the [AWS Management Console](https://console.aws.amazon.com)
2. In the search bar at the top, type "S3" and select the "S3" service from the dropdown
3. This will take you to the S3 dashboard where you can see all your existing buckets (if any)

### Step 2: Create a New Bucket
1. Click the orange **"Create bucket"** button
2. In the "Create bucket" form, you'll need to provide several details:

   a. **General configuration**:
   - **Bucket name**: Enter `lab02-yourname-YYYYMMDD` (replace "yourname" with your name and YYYYMMDD with current date)
     - Remember: Bucket names must be globally unique across all AWS accounts
     - Use only lowercase letters, numbers, hyphens, and periods
   - **AWS Region**: Bucket will be created in the region you are currently working on
   
   b. **Object Ownership**:
   - Leave the default setting (ACLs disabled)
   
   c. **Block Public Access settings**:
   - For now, leave "Block all public access" checked (we'll modify this later)
   
   d. **Bucket Versioning**:
   - Leave disabled for this lab (we'll explore this in a later lab)
   
   e. **Default encryption**:
   - Leave the default setting (Amazon S3 managed keys)
   
3. Click **"Create bucket"** at the bottom of the page

### Step 3: Verify Bucket Creation
1. After clicking "Create bucket," you'll be returned to the S3 dashboard
2. You should see your new bucket in the list with "Created" date showing today
3. The bucket is now ready to store objects (files)

## 📁 Part 2: Upload and Manage Files (Console)

### Step 1: Upload a File
1. Click on the name of your bucket to open it
2. You should see an empty bucket with no objects
3. Click the **"Upload"** button
4. Click **"Add files"** and select a small file from your computer (like a text file or image)
5. Leave all default settings and click **"Upload"** at the bottom
6. Wait for the upload to complete (you'll see a success message)
7. Your file should now appear in the bucket's object list

### Step 2: Explore Object Properties
1. Click on the name of the uploaded file
2. This opens the object overview page where you can see:
   - Object URL (not accessible yet due to permissions)
   - Storage class
   - Last modified date
   - Size
   - Encryption settings
3. Notice the **"Open"** and **"Download"** buttons at the top
4. Clicking "Open" - you'll be able to see the file content

### Step 3: Create and Upload an HTML File for Website Hosting
1. On your computer, create a simple HTML file named `index.html` with the following content (replace '<YOUR-NAME>' with your actual name):
   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>My S3 Website</title>
       <style>
           body {
               font-family: Arial, sans-serif;
               line-height: 1.6;
               margin: 0;
               padding: 30px;
               background-color: #f4f4f4;
               color: #333;
           }
           .container {
               max-width: 800px;
               margin: 0 auto;
               background: white;
               padding: 20px;
               border-radius: 5px;
               box-shadow: 0 0 10px rgba(0,0,0,0.1);
           }
           h1 {
               color: #0066cc;
           }
       </style>
   </head>
   <body>
       <div class="container">
           <h1>Welcome to <YOUR-NAME> website</h1>
           <p>This is a static website hosted on Amazon S3.</p>
           <p>Current time when this page was accessed: <span id="datetime"></span></p>
           <script>
               document.getElementById("datetime").textContent = new Date().toLocaleString();
           </script>
       </div>
   </body>
   </html>
   ```

2. Return to your bucket in the AWS Console and upload this file following the same steps as before

## 🔐 Part 3: Configure Bucket Policies

### Step 1: Understand Bucket Policies
A bucket policy is a JSON document that defines access permissions to your bucket and the objects inside it. Let's create a policy that makes our HTML file publicly accessible.

### Step 2: Modify Block Public Access Settings
1. At the top of the "Permissions" tab, find "Block public access (bucket settings)"
2. Click **"Edit"**
3. Uncheck "Block all public access"
4. A warning will appear - read it carefully to understand the implications
5. Type "confirm" in the confirmation field
6. Click **"Save changes"**

### Step 3: Add a Bucket Policy
1. Navigate to your bucket and click the **"Permissions"** tab
2. Scroll down to the "Bucket policy" section and click **"Edit"**
3. Enter the following policy in the editor (replace `YOUR-BUCKET-NAME` with your actual bucket name):
   ```json
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Sid": "PublicReadGetObject",
               "Effect": "Allow",
               "Principal": "*",
               "Action": "s3:GetObject",
               "Resource": "arn:aws:s3:::YOUR-BUCKET-NAME/*"
           }
       ]
   }
   ```
4. This policy allows anyone to read (GET) objects from your bucket
5. Click **"Save changes"**

### Step 4: Test Public Access
1. Return to the "Objects" tab
2. Click on your `index.html` file
3. Now try clicking the **"Open"** button again
4. This time the file should open in a new browser tab and you should see the HTML content

## 🌐 Part 4: Enable Static Website Hosting

### Step 1: Configure Website Hosting
1. Return to your bucket and click on the **"Properties"** tab
2. Scroll down to the bottom to find "Static website hosting"
3. Click **"Edit"**
4. Select the option for "Enable"
5. Set the following values:
   - **Hosting type**: "Host a static website"
   - **Index document**: `index.html`
   - **Error document**: Leave empty for now
6. Click **"Save changes"**

### Step 2: Access Your Website
1. Scroll back to the "Static website hosting" section
2. You'll now see a "Bucket website endpoint" - this is the URL to your website
3. Click on this URL or copy-paste it into a new browser tab
4. You should see your HTML page displayed as a proper website

## 💻 Part 5: Work with S3 Using AWS CLI

Now let's perform the same operations using command-line tools.

### Step 1: Create a New Bucket with CLI
Open your terminal or command prompt and run:

```bash
aws s3 mb s3://lab02-cli-bucket-YYYYMMDD --region eu-west-1
```
Replace YYYYMMDD with the current date (e.g., 20230430). The command will create a new S3 bucket.

### Step 2: Create and Upload a File
```bash
# Create a simple text file
echo "This file was uploaded using the AWS CLI" > cli-test.txt

# Upload the file to your bucket
aws s3 cp cli-test.txt s3://lab02-cli-bucket-YYYYMMDD/
```

### Step 3: List Objects in the Bucket
```bash
aws s3 ls s3://lab02-cli-bucket-YYYYMMDD/
```
You should see your `cli-test.txt` file in the output.

### Step 4: Download a File from S3
```bash
# Create a directory for downloaded files
mkdir s3-downloads

# Download the file
aws s3 cp s3://lab02-cli-bucket-YYYYMMDD/cli-test.txt s3-downloads/downloaded-file.txt

# Verify the content
cat s3-downloads/downloaded-file.txt
```

### Step 5: Make Bucket Public
```bash
aws s3api put-public-access-block --bucket lab02-cli-bucket-YYYYMMDD --public-access-block-configuration "BlockPublicAcls=false,IgnorePublicAcls=false,BlockPublicPolicy=false,RestrictPublicBuckets=false"
```

### Step 6: Apply a Bucket Policy Using CLI
Create a file named `bucket-policy.json` with the following content (replace with your actual bucket name):

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicReadGetObject",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::lab02-cli-bucket-YYYYMMDD/*"
        }
    ]
}
```

Apply the policy to your bucket:
```bash
aws s3api put-bucket-policy --bucket lab02-cli-bucket-YYYYMMDD --policy file://bucket-policy.json
```

### Step 7: Enable Static Website Hosting via CLI
```bash
aws s3 website s3://lab02-cli-bucket-YYYYMMDD/ --index-document index.html
```

### Step 8: Upload an HTML file
Create another file called `index.html` with similar content to the one you created earlier, then upload it:

```bash
aws s3 cp index.html s3://lab02-cli-bucket-YYYYMMDD/
```

Access your website at: `http://lab02-cli-bucket-YYYYMMDD.s3-website-eu-west-1.amazonaws.com`

## 🧪 Challenge Tasks

Now that you've learned the basics, try these additional challenges:

### Challenge 1: Custom Error Page
1. Create an `error.html` file with custom error content
2. Upload it to your bucket
3. Update the static website hosting settings to use this file as the error document
4. Test by accessing a non-existent file in your bucket

### Challenge 2: Folder Structure
1. Create a folder structure in your bucket (e.g., `images/`, `css/`, `js/`)
2. Upload different types of files to appropriate folders
3. Update your index.html to reference files in these folders
4. Test that everything works correctly

### Challenge 3: Restrictive Bucket Policy
1. Modify your bucket policy to only allow GET access to specific files (like `index.html`)
2. Test by trying to access different files in your bucket

## 🔍 Troubleshooting Common Issues

### Access Denied Errors
- Check that your bucket policy is correctly formatted
- Verify Block Public Access settings are disabled
- Ensure the bucket policy references the correct bucket name

### Website Not Accessible
- Confirm that static website hosting is enabled
- Verify that the index document matches your uploaded file name exactly
- Check that both bucket policy and public access settings are configured

### File Not Found
- Verify you used the correct bucket name in your commands
- Check that the file path/name in your command matches the uploaded file
- List the contents of the bucket to verify the file exists

## 🌀 Cleanup - IMPORTANT!

To avoid ongoing charges, clean up all resources created in this lab:

### Using Console:
1. Navigate to your bucket
2. Select all objects and click "Delete"
3. Type "permanently delete" when prompted
4. After all objects are deleted, go back to the buckets list
5. Select your bucket and click "Delete"
6. Type your bucket name to confirm deletion

### Using CLI:
```bash
# Delete all objects in the bucket
aws s3 rm s3://lab02-cli-bucket-YYYYMMDD/ --recursive

# Delete the bucket itself
aws s3 rb s3://lab02-cli-bucket-YYYYMMDD

# Delete the console-created bucket too
aws s3 rm s3://lab02-yourname-YYYYMMDD/ --recursive
aws s3 rb s3://lab02-yourname-YYYYMMDD
```

## ✅ What You've Learned

In this lab, you have:

1. Created S3 buckets using both the AWS Console and AWS CLI
2. Uploaded, downloaded, and managed objects (files) in S3
3. Configured bucket policies to control access to your resources
4. Enabled and used static website hosting
5. Used the AWS CLI to interact with S3 programmatically
6. Properly cleaned up resources to prevent ongoing charges

These skills form the foundation for using AWS storage services in your applications, from static website hosting to application data storage and backup systems.

## 📚 Additional Resources

- [Amazon S3 User Guide](https://docs.aws.amazon.com/AmazonS3/latest/userguide/Welcome.html)
- [S3 Bucket Policy Examples](https://docs.aws.amazon.com/AmazonS3/latest/userguide/example-bucket-policies.html)
- [AWS CLI S3 Commands Reference](https://docs.aws.amazon.com/cli/latest/reference/s3/index.html)
- [S3 Static Website Hosting Documentation](https://docs.aws.amazon.com/AmazonS3/latest/userguide/WebsiteHosting.html)

---

Happy Clouding ☁️!

