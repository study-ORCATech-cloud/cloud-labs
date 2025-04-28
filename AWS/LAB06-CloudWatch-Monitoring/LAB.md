# LAB06: Monitor EC2 with CloudWatch Logs and Alarms (AWS Console & CLI)

## 📊 Lab Overview

In this lab, you'll enable detailed monitoring and logging for an EC2 instance using **Amazon CloudWatch**. You'll collect operating system logs and custom metrics with the **CloudWatch Agent**, visualize performance data in dashboards, and set up alarms for automatic notification when thresholds are breached. This lab represents real-world DevOps monitoring practices essential for maintaining reliable production systems.

### 👨‍💻 Why Monitoring Matters in DevOps

Monitoring is a critical pillar of DevOps for several reasons:
- **Early Problem Detection**: Identify issues before they affect users
- **Performance Optimization**: Track resource utilization to right-size infrastructure
- **Cost Management**: Monitor usage patterns to identify optimization opportunities
- **Security Compliance**: Keep audit logs for security investigation
- **Automated Response**: Enable self-healing systems through alarm-triggered actions

---

## 🎯 Objectives

By the end of this lab, you will be able to:
- Install and configure the CloudWatch Agent on an EC2 instance
- Send system logs and custom metrics to CloudWatch
- Create and customize CloudWatch dashboards for visualization
- Analyze log patterns with CloudWatch Logs Insights
- Create and test CloudWatch alarms for high CPU usage
- Implement basic monitoring using AWS CLI for automation

---

## 🛠️ Prerequisites

- AWS account with administrative permissions
- Basic understanding of EC2 instances and IAM roles
- AWS CLI installed and configured (refer to [CLI Setup Guide](../../CLI-Setup.md))
- Understanding of basic Linux commands

---

## 🖥️ Part 1: Setup EC2 & IAM Role

### Step 1: Create IAM Role for CloudWatch

1. Navigate to **IAM > Roles** in the AWS Management Console
2. Click **Create role**
3. Select **AWS service** as the trusted entity type
4. Choose **EC2** as the use case
5. Click **Next: Permissions**
6. Search for and select the `CloudWatchAgentServerPolicy` managed policy
7. (Optional) Add `AmazonSSMManagedInstanceCore` for Systems Manager access
8. Click **Next: Tags**, add optional tags
9. Click **Next: Review**
10. Name the role `EC2CloudWatchRole` and provide a description
11. Click **Create role**

### Step 2: Launch EC2 Instance with the IAM Role

1. Navigate to **EC2 > Instances** in the AWS Management Console
2. Click **Launch Instances**
3. Name your instance `CloudWatchDemo`
4. Select **Amazon Linux 2** as the AMI
5. Choose **t2.micro** as the instance type (free tier eligible)
6. Under **Key pair**, select an existing key pair or create a new one
7. Under **Network settings**, allow SSH traffic from your IP
8. Under **Advanced details**, select the `EC2CloudWatchRole` IAM role you created
9. Click **Launch instance**
10. Wait for the instance to reach the "Running" state

### Step 3: Connect to Your Instance

1. Select your instance in the EC2 console
2. Click **Connect** and follow the instructions to connect via SSH
   ```bash
   ssh -i /path/to/key.pem ec2-user@your-instance-public-ip
   ```

---

## ⚙️ Part 2: Install & Configure CloudWatch Agent

### Step 1: Install the CloudWatch Agent

```bash
# Update packages
sudo yum update -y

# Install the CloudWatch Agent
sudo yum install -y amazon-cloudwatch-agent

# Verify installation
amazon-cloudwatch-agent-ctl -h
```

### Step 2: Configure the Agent using the Configuration Wizard

The wizard will guide you through selecting metrics and logs to collect:

```bash
# Start the configuration wizard
sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-config-wizard
```

When prompted, select the following options:
1. Choose `1` (EC2 instance monitoring)
2. Choose `1` (Basic monitoring) or `2` (Standard monitoring) based on your needs
3. Select `Yes` for detailed monitoring metrics (CPU per core, disk I/O)
4. Select `Yes` to collect memory metrics
5. Select both root and data disk metrics if applicable
6. Select `Yes` to collect log files
7. Add log file path: `/var/log/messages`
8. Add another log file path: `/var/log/secure` (for SSH connection logs)
9. Choose `1` (Default configuration) for CloudWatch log group name or customize
10. Specify a region or leave default
11. Choose `1` (No proxy) unless your environment uses a proxy

### Step 3: Start the Agent with Your Configuration

```bash
# Start the agent with the created configuration
sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl \
  -a fetch-config -m ec2 -c file:/opt/aws/amazon-cloudwatch-agent/bin/config.json -s

# Verify the agent is running
sudo systemctl status amazon-cloudwatch-agent
```

### Step 4: Generate Some Metrics and Logs

Let's create some activity to monitor:

```bash
# Generate CPU load
stress --cpu 1 --timeout 60s &

# Generate log entries
logger "CloudWatch Lab - Test log entry"
logger "CloudWatch Lab - ERROR: Simulated error message"
logger "CloudWatch Lab - WARNING: Simulated warning message"

# View your log entries
tail /var/log/messages
```

> Note: If `stress` is not installed, you can install it with `sudo yum install -y stress`

---

## 📈 Part 3: Visualize Metrics and Logs

### Step 1: Explore CloudWatch Logs

1. Navigate to **CloudWatch > Logs > Log groups** in the AWS Management Console
2. You should see log groups created by the CloudWatch Agent
3. Click on a log group, then click on a log stream
4. Explore the log entries captured from your EC2 instance
5. Try using the search functionality to find your test error messages

### Step 2: Use CloudWatch Logs Insights

1. In CloudWatch, go to **Logs > Logs Insights**
2. Select the log group for your `/var/log/messages`
3. Try the following query to find error messages:
   ```
   fields @timestamp, @message
   | filter @message like /ERROR/
   | sort @timestamp desc
   | limit 20
   ```
4. Click **Run query** and examine the results

### Step 3: View Custom Metrics

1. Navigate to **CloudWatch > Metrics** in the AWS Management Console
2. Find and select the **CWAgent** namespace
3. Click on **Instance, InstanceId, ImageId, InstanceType**
4. Check the box next to metrics like `cpu_usage_idle` and `mem_used_percent`
5. Observe the graph showing your instance's metrics
6. Experiment with the graph settings (time range, statistics, period)

### Step 4: Create a Dashboard

1. Navigate to **CloudWatch > Dashboards**
2. Click **Create dashboard**
3. Name it `EC2-Monitoring-Dashboard` and click **Create**
4. Add a widget: select **Line** as the visualization type
5. Search for your instance metrics and add CPU, memory, and disk metrics
6. Add a **Log widget** for your log group
7. Arrange widgets as desired and click **Save dashboard**

---

## 🔔 Part 4: Create Alarm

### Step 1: Create a CPU Utilization Alarm

1. Navigate to **CloudWatch > Alarms > All alarms**
2. Click **Create alarm**
3. Click **Select metric**
4. Navigate to **CWAgent > ImageId, InstanceId, InstanceType, instance**
5. Select the `cpu_usage_idle` metric for your instance
6. Click **Select metric**
7. Change the statistic to **Average** and period to **1 minute**
8. Under **Conditions**, select "Lower/Equal" and enter `50` as the threshold
   (This triggers when CPU is less than 50% idle, meaning CPU usage is over 50%)
9. Click **Next**
10. Under **Notification**, select **Create new topic**
11. Enter topic name `HighCPUAlarm` and your email address
12. Click **Create topic**
13. Click **Next**
14. Enter an alarm name `HighCPUUsage-EC2Instance` and description
15. Click **Next**
16. Review and click **Create alarm**

### Step 2: Test the Alarm

1. Confirm the subscription email you'll receive from SNS
2. SSH back into your instance
3. Generate high CPU load:
   ```bash
   # Run a CPU-intensive task
   stress --cpu 1 --timeout 300s &
   ```
4. Monitor the alarm in the CloudWatch console
5. You should receive an email when the alarm status changes to ALARM
6. Let the stress test complete and verify the alarm returns to OK state

---

## 🧪 Challenge: Advanced Monitoring

Complete these additional tasks to expand your CloudWatch expertise:

### Challenge 1: Monitor Disk Space

Configure a CloudWatch alarm for disk usage exceeding 70%:

1. Find the disk usage metric in CloudWatch
2. Create an alarm with appropriate threshold
3. Document the steps you took in a file called `disk-alarm-setup.md`

### Challenge 2: Monitor Custom Application Logs

1. Create a simple application log by running:
   ```bash
   # Create application log directory
   sudo mkdir -p /var/log/myapp
   
   # Generate some log entries
   for i in {1..20}; do
     if [ $((i % 5)) -eq 0 ]; then
       sudo sh -c "echo '$(date) - ERROR: Database connection failed' >> /var/log/myapp/app.log"
     else
       sudo sh -c "echo '$(date) - INFO: User $i logged in successfully' >> /var/log/myapp/app.log"
     fi
   done
   ```

2. Update the CloudWatch agent config to monitor this new log file:
   ```bash
   sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-config-wizard
   ```

3. When prompted to add log files, add `/var/log/myapp/app.log`

4. Restart the agent and verify logs appear in CloudWatch

---

## 💻 Part 5: CloudWatch Operations with AWS CLI

Here's how to perform the same monitoring tasks using AWS CLI for automation:

### Step 1: View CloudWatch Metrics via CLI

```bash
# List available metrics for your instance
aws cloudwatch list-metrics --namespace CWAgent --dimensions Name=InstanceId,Value=YOUR_INSTANCE_ID

# Get CPU utilization statistics
aws cloudwatch get-metric-statistics \
  --namespace CWAgent \
  --metric-name cpu_usage_idle \
  --dimensions Name=InstanceId,Value=YOUR_INSTANCE_ID \
  --start-time $(date -u -d '1 hour ago' +%Y-%m-%dT%H:%M:%SZ) \
  --end-time $(date -u +%Y-%m-%dT%H:%M:%SZ) \
  --period 300 \
  --statistics Average
```

### Step 2: Create an Alarm via CLI

```bash
# Create an SNS topic for notifications
aws sns create-topic --name CloudWatchAlarmTopic

# Subscribe your email to the topic
aws sns subscribe \
  --topic-arn YOUR_TOPIC_ARN \
  --protocol email \
  --notification-endpoint your.email@example.com

# Create a CPU alarm
aws cloudwatch put-metric-alarm \
  --alarm-name HighCPU-CLI \
  --alarm-description "Alarm when CPU exceeds 70%" \
  --metric-name cpu_usage_idle \
  --namespace CWAgent \
  --statistic Average \
  --period 60 \
  --threshold 30 \
  --comparison-operator LessThanOrEqualToThreshold \
  --dimensions Name=InstanceId,Value=YOUR_INSTANCE_ID \
  --evaluation-periods 2 \
  --alarm-actions YOUR_TOPIC_ARN
```

### Step 3: Query CloudWatch Logs via CLI

```bash
# Query log group for error messages
aws logs start-query \
  --log-group-name /var/log/messages \
  --start-time $(date -d '1 hour ago' +%s) \
  --end-time $(date +%s) \
  --query-string 'fields @timestamp, @message | filter @message like /ERROR/'

# Get query results (use the queryId from the previous command)
aws logs get-query-results --query-id YOUR_QUERY_ID
```

---

## 🧹 Cleanup

To avoid incurring charges after completing this lab, clean up all resources:

```bash
# Stop and remove the CloudWatch agent
sudo systemctl stop amazon-cloudwatch-agent
sudo yum remove -y amazon-cloudwatch-agent

# Delete CloudWatch alarms
aws cloudwatch delete-alarms --alarm-names HighCPUUsage-EC2Instance HighCPU-CLI

# Delete CloudWatch dashboard
aws cloudwatch delete-dashboards --dashboard-names EC2-Monitoring-Dashboard

# Delete SNS topics
aws sns delete-topic --topic-arn YOUR_TOPIC_ARN

# Terminate the EC2 instance via Console or CLI
aws ec2 terminate-instances --instance-ids YOUR_INSTANCE_ID
```

Additionally, log groups will continue to store logs indefinitely by default:

1. Navigate to **CloudWatch > Logs > Log groups**
2. Select each log group created during this lab
3. Click **Actions > Delete log group(s)**

---

## ✅ Key Takeaways

In this lab, you've learned how to:

- Configure an EC2 instance for complete observability using CloudWatch
- Collect and analyze both system and application logs
- Set up metric-based alarms for proactive monitoring
- Create dashboards for visualizing system performance
- Use CloudWatch Logs Insights for log analysis
- Automate monitoring tasks using AWS CLI

These skills are essential for building reliable, observable systems in AWS, allowing you to detect and resolve issues before they impact your users.

## 📚 Additional Resources

- [CloudWatch User Guide](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/WhatIsCloudWatch.html)
- [CloudWatch Agent Documentation](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/Install-CloudWatch-Agent.html)
- [CloudWatch Logs Insights Syntax](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/CWL_QuerySyntax.html)
- [CloudWatch Dashboards](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch_Dashboards.html)
- [Best Practices for Monitoring](https://aws.amazon.com/blogs/mt/best-practices-for-monitoring-your-aws-environment/)

---

Happy Monitoring! 📊

