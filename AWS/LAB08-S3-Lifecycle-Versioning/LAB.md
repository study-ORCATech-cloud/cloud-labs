# LAB08: Implement S3 Lifecycle Rules and Versioning (Console & CLI)

## 🔁 Lab Overview

In this lab, you'll automate data retention and version control using **Amazon S3**. You'll enable **versioning**, create **lifecycle policies**, and explore how to manage object aging, transitions, and deletions. These features help reduce storage costs while ensuring data recoverability, critical capabilities for any data management strategy in the cloud.

### 📋 Real-World DevOps Scenario

In production environments, DevOps teams face common data management challenges:

- **Cost Management**: Storing all data in high-performance storage tiers becomes expensive at scale
- **Compliance Requirements**: Many industries require retention of data for specific periods
- **Disaster Recovery**: Protecting against accidental deletions or corruptions
- **Storage Administration**: Manual management of large data stores is time-consuming

S3 Lifecycle policies and versioning solve these challenges by automating the management of data throughout its lifecycle. For example:

- **Media Company**: Automatically moves older video assets to cold storage after 90 days
- **Financial Institution**: Retains transaction records for 7 years before deletion, with different storage tiers
- **Web Application**: Keeps backups of user-uploaded content with version history for recovery

---

## 🎯 Objectives

By the end of this lab, you will be able to:
- Create and configure an S3 bucket with versioning enabled
- Upload, modify, and restore different versions of objects
- Implement lifecycle rules to automate storage transitions
- Configure expiration policies for cost optimization
- Use both the AWS Console and CLI to manage versioning and lifecycle rules
- Monitor the impact of lifecycle policies on storage costs

---

## 🛠️ Prerequisites

- AWS account with administrative permissions
- Basic understanding of S3 buckets and objects
- AWS CLI installed and configured (refer to [CLI Setup Guide](../../CLI-Setup.md))
- Text editor for creating sample files and viewing lifecycle configurations

---

## 📂 Part 1: Create a Versioned S3 Bucket

### Step 1: Create an S3 Bucket with Versioning Enabled

1. Sign in to the AWS Management Console
2. Navigate to **Amazon S3** service
3. Click **Create bucket**
4. Configure the bucket:
   - **Bucket name**: `lab08-versioned-bucket-<yourname>` (replace `<yourname>` with your name or unique identifier)
   - **AWS Region**: `eu-west-1` (or your preferred region)
   - **Object Ownership**: ACLs disabled (recommended)
   - **Block Public Access settings**: Keep all enabled (default)
   - **Bucket Versioning**: Enable
   - **Default encryption**: Enable with Amazon S3-managed keys (SSE-S3)
5. Click **Create bucket**

### Step 2: Upload Initial Objects

1. Click on your newly created bucket name
2. Click **Upload**
3. Create a simple text file on your local machine:
   ```
   This is version 1 of notes.txt
   Created for the S3 lifecycle and versioning lab
   ```
4. Save the file as `notes.txt`
5. Either drag this file to the upload area or click **Add files** and select it
6. Leave default settings and click **Upload**
7. You should see a success message once the upload completes

### Step 3: Create and Upload Modified Versions

1. Edit `notes.txt` on your local machine and change its content:
   ```
   This is version 2 of notes.txt
   I've made some changes to the file
   Created for the S3 lifecycle and versioning lab
   ```
2. Save the file and upload it again (using the same steps as above)
3. Edit and upload a third version:
   ```
   This is version 3 of notes.txt
   I've made even more changes to the file
   This demonstrates how versioning works in S3
   Created for the S3 lifecycle and versioning lab
   ```

### Step 4: View Version History

1. In your bucket, locate `notes.txt` in the object list
2. Click on the filename to open its details page
3. Click on the **Versions** tab
4. You should see all three versions listed with timestamps
5. The most recent version is labeled as "Current version"
6. Click on the Version ID of an older version to view or download it

### Step 5: Delete and Restore an Object

1. From the bucket object list, select `notes.txt`
2. Click **Delete**
3. Confirm deletion by typing "delete" in the confirmation field
4. Click **Delete objects**
5. Notice the object appears to be gone from the main view
6. Enable **Show versions** toggle in the bucket view (upper right)
7. You should now see all versions including a "Delete marker"
8. To restore the file, delete the "Delete marker":
   - Select the Delete marker (latest version with the delete icon)
   - Click **Delete**
   - Confirm the deletion
9. The most recent version before deletion is now restored as the current version

---

## 🔁 Part 2: Configure Lifecycle Rules (Console)

### Step 1: Create a Basic Lifecycle Rule

1. Navigate to your bucket
2. Click the **Management** tab
3. Scroll down to **Lifecycle rules** and click **Create lifecycle rule**
4. Configure the rule:
   - **Lifecycle rule name**: `transition-old-versions`
   - **Rule scope**: Apply to all objects in the bucket
   - **Lifecycle rule actions**:
     - Check **Transition noncurrent versions of objects between storage classes**
     - Check **Permanently delete noncurrent versions of objects**
5. Under **Transition noncurrent versions of objects between storage classes**:
   - **Storage class transitions**:
     - Choose **Glacier Instant Retrieval**
     - Days after objects become noncurrent: `30`
   - Add another transition (click **Add transition**)
     - Choose **Glacier Flexible Retrieval**
     - Days after objects become noncurrent: `60`
6. Under **Permanently delete noncurrent versions of objects**:
   - Days after objects become noncurrent: `365`
   - (Note: Using 365 days for lab purposes - in production, this might be much longer)
7. Click **Create rule**

### Step 2: Create a Tag-Based Lifecycle Rule

1. Return to the **Management** tab and lifecycle rules section
2. Click **Create lifecycle rule**
3. Configure the rule:
   - **Lifecycle rule name**: `archive-tagged-objects`
   - **Rule scope**: Limit the scope to specific object tags
   - **Tag**: Add tag with Key: `archive` and Value: `true`
   - **Lifecycle rule actions**:
     - Check **Transition current versions of objects between storage classes**
4. Under **Transition current versions of objects between storage classes**:
   - **Storage class transitions**:
     - Choose **Standard-IA**
     - Days after object creation: `30`
   - Add another transition
     - Choose **Glacier Instant Retrieval**
     - Days after object creation: `90`
5. Click **Create rule**

### Step 3: Apply Tags to Objects

1. Go back to the bucket objects list
2. Create a new text file on your local machine named `archive_document.txt`
3. Upload this file to your bucket
4. Select the file in the bucket
5. Click **Actions** and select **Edit tags**
6. Click **Add tag**
7. Enter Key: `archive` and Value: `true`
8. Click **Save changes**

---

## 💻 Part 3: Manage Versioning and Lifecycle Rules via CLI

The AWS CLI allows you to automate these processes in scripts or CI/CD pipelines.

### Step 1: Check Bucket Versioning Status

```bash
# Check current versioning status
aws s3api get-bucket-versioning --bucket lab08-versioned-bucket-<yourname>
```

### Step 2: Enable Versioning on a Bucket

```bash
# Enable versioning (if not already enabled)
aws s3api put-bucket-versioning \
  --bucket lab08-versioned-bucket-<yourname> \
  --versioning-configuration Status=Enabled
```

### Step 3: Upload Multiple Versions Using CLI

```bash
# Create and upload first version
echo "This is CLI version 1" > cli_notes.txt
aws s3 cp cli_notes.txt s3://lab08-versioned-bucket-<yourname>/cli_notes.txt

# Create and upload second version
echo "This is CLI version 2 with changes" > cli_notes.txt
aws s3 cp cli_notes.txt s3://lab08-versioned-bucket-<yourname>/cli_notes.txt

# List all versions of the object
aws s3api list-object-versions \
  --bucket lab08-versioned-bucket-<yourname> \
  --prefix cli_notes.txt
```

### Step 4: Create Lifecycle Configuration JSON

Create a file named `lifecycle.json` with the following content:

```bash
cat > lifecycle.json << EOF
{
  "Rules": [
    {
      "ID": "TransitionAndExpireNoncurrentVersions",
      "Status": "Enabled",
      "Filter": {
        "Prefix": ""
      },
      "NoncurrentVersionTransitions": [
        {
          "NoncurrentDays": 30,
          "StorageClass": "GLACIER_IR"
        },
        {
          "NoncurrentDays": 60,
          "StorageClass": "GLACIER"
        }
      ],
      "NoncurrentVersionExpiration": {
        "NoncurrentDays": 365
      }
    },
    {
      "ID": "ArchiveTaggedObjects",
      "Status": "Enabled",
      "Filter": {
        "Tag": {
          "Key": "archive",
          "Value": "true"
        }
      },
      "Transitions": [
        {
          "Days": 30,
          "StorageClass": "STANDARD_IA"
        },
        {
          "Days": 90,
          "StorageClass": "GLACIER_IR"
        }
      ]
    }
  ]
}
EOF
```

### Step 5: Apply Lifecycle Configuration

```bash
# Apply the lifecycle configuration
aws s3api put-bucket-lifecycle-configuration \
  --bucket lab08-versioned-bucket-<yourname> \
  --lifecycle-configuration file://lifecycle.json

# Verify the configuration
aws s3api get-bucket-lifecycle-configuration \
  --bucket lab08-versioned-bucket-<yourname>
```

### Step 6: Add Tags to Objects via CLI

```bash
# Add archive tag to an object
aws s3api put-object-tagging \
  --bucket lab08-versioned-bucket-<yourname> \
  --key cli_notes.txt \
  --tagging 'TagSet=[{Key=archive,Value=true}]'

# Verify tags
aws s3api get-object-tagging \
  --bucket lab08-versioned-bucket-<yourname> \
  --key cli_notes.txt
```

---

## 🧪 Challenge: Advanced Lifecycle Management

Complete these additional tasks to expand your S3 lifecycle management knowledge:

### Challenge 1: Implement Multipart Upload Cleanup

Incomplete multipart uploads can accumulate and incur storage costs. Configure a lifecycle rule to:

1. Return to the **Management** tab and lifecycle rules section
2. Create a new rule named `cleanup-incomplete-uploads`
3. Apply to all objects
4. Select the **Delete expired object delete markers or incomplete multipart uploads** action
5. Under **Delete incomplete multipart uploads**:
   - Days after initiation: `7`
6. Create the rule and document why this is important for cost management

### Challenge 2: Restore an Object from Glacier

For this challenge, you'll need to wait until a lifecycle rule has transitioned an object to Glacier. Since that takes time in a real environment, simulate this by:

1. Upload a new file to your bucket
2. Use the console to manually change its storage class to Glacier:
   - Select the file
   - Click **Actions > Edit storage class**
   - Select **Glacier Instant Retrieval**
   - Save changes
3. Now restore this object:
   - Select the file
   - Click **Actions > Restore from S3 Glacier**
   - For Glacier Instant Retrieval, restoration is immediate
   - For Glacier Flexible Retrieval, specify a restoration period (e.g., 10 days)
4. Document the steps and the differences between Glacier storage classes

### Challenge 3: Analyze Storage Costs

1. Use the AWS Pricing Calculator (https://calculator.aws/) to:
   - Estimate costs for 100GB of data with various lifecycle scenarios:
     - Scenario 1: All data in S3 Standard
     - Scenario 2: Data transitions to S3-IA after 30 days, then to Glacier after 90 days
   - Document the cost differences and savings percentage

---

## 📈 Part 4: Monitoring and Verification

### Step 1: View Storage Class Distribution

1. Navigate to your bucket and click the **Metrics** tab
2. Click **Create filter**
3. Leave the filter empty to include all objects
4. Click **Save changes**
5. After some time (24-48 hours in a real environment), you can see storage distribution

### Step 2: Explore Lifecycle Transition Markers

As objects transition between storage classes, you can verify this is happening:

```bash
# List all versions with storage class information
aws s3api list-object-versions \
  --bucket lab08-versioned-bucket-<yourname> \
  --query 'Versions[].{Key:Key,VersionId:VersionId,StorageClass:StorageClass}'
```

Note: In a lab setting, you won't see actual transitions since they take time to process, but this command shows how to verify them in a production environment.

---

## 🧹 Cleanup

To avoid incurring charges after completing this lab, clean up all resources:

### Console Cleanup:

1. Navigate to your bucket
2. Enable **Show versions** toggle to see all versions
3. Select all objects and versions (including delete markers)
4. Click **Delete**
5. Confirm deletion by typing "permanently delete" in the confirmation field
6. Once all objects are deleted, go to the bucket list
7. Select your bucket
8. Click **Delete**
9. Confirm deletion by typing your bucket name

### CLI Cleanup:

```bash
# Delete all object versions and delete markers
aws s3api list-object-versions \
  --bucket lab08-versioned-bucket-<yourname> \
  --output json \
  --query '{Objects: Versions[].{Key:Key,VersionId:VersionId}}' > versions.json

# Check if any versions were found and delete them
if [ $(grep -c VersionId versions.json) -gt 0 ]; then
  aws s3api delete-objects \
    --bucket lab08-versioned-bucket-<yourname> \
    --delete file://versions.json
fi

# Delete delete markers
aws s3api list-object-versions \
  --bucket lab08-versioned-bucket-<yourname> \
  --output json \
  --query '{Objects: DeleteMarkers[].{Key:Key,VersionId:VersionId}}' > markers.json

# Check if any delete markers were found and delete them
if [ $(grep -c VersionId markers.json) -gt 0 ]; then
  aws s3api delete-objects \
    --bucket lab08-versioned-bucket-<yourname> \
    --delete file://markers.json
fi

# Finally, delete the bucket
aws s3api delete-bucket \
  --bucket lab08-versioned-bucket-<yourname>

# Clean up local files
rm -f lifecycle.json versions.json markers.json notes.txt cli_notes.txt
```

---

## ✅ Key Takeaways

In this lab, you've learned:

- How to enable and use S3 bucket versioning for change tracking and recovery
- How to implement lifecycle rules to automate storage tier transitions
- How to configure different rules based on object tags, prefixes, or age
- How to expire old versions to optimize storage costs
- How to restore objects from older versions or after deletion
- The significant cost advantages of proper lifecycle management in production environments

These skills are essential for building cost-effective and automated storage solutions in the cloud, allowing your data to be stored at the appropriate cost tier based on its access patterns and importance.

## 📚 Additional Resources

- [S3 Versioning Documentation](https://docs.aws.amazon.com/AmazonS3/latest/userguide/Versioning.html)
- [S3 Lifecycle Management Guide](https://docs.aws.amazon.com/AmazonS3/latest/userguide/object-lifecycle-mgmt.html)
- [S3 Storage Classes Comparison](https://aws.amazon.com/s3/storage-classes/)
- [S3 Pricing](https://aws.amazon.com/s3/pricing/)
- [Best Practices for S3 Cost Optimization](https://aws.amazon.com/blogs/storage/best-practices-for-amazon-s3-cost-optimization/)

---

Happy Cloud Storage Management! 🪣

