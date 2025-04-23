# LAB06: Monitor EC2 with CloudWatch Logs and Alarms (AWS Console & CLI)

## 📊 Lab Overview

In this lab, you’ll enable detailed monitoring and logging for an EC2 instance using **Amazon CloudWatch**. You’ll collect OS logs and metrics with the **CloudWatch Agent**, visualize data in CloudWatch, and set up an alarm for automatic alerting.

---

## 🎯 Objectives

- Install and configure CloudWatch Agent on EC2
- Send system logs and metrics to CloudWatch
- Visualize logs and CPU utilization
- Create a CloudWatch alarm for high CPU usage

---

## 🛠️ Prerequisites

- AWS account
- EC2 instance with IAM role allowing CloudWatch write access
- AWS CLI and Systems Manager (SSM) Agent installed

---

## 🖥️ Part 1: Setup EC2 & IAM Role

1. Launch EC2 instance (Amazon Linux 2)
2. Create IAM role with policy `CloudWatchAgentServerPolicy`
3. Attach IAM role to EC2 instance

---

## ⚙️ Part 2: Install & Configure CloudWatch Agent

```bash
sudo yum install -y amazon-cloudwatch-agent
sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-config-wizard
```
Answer prompts to collect:
- CPU
- Memory
- Disk
- Logs: `/var/log/messages`

Start the agent:
```bash
sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl \
  -a fetch-config -m ec2 -c file:/opt/aws/amazon-cloudwatch-agent/bin/config.json -s
```

---

## 📈 Part 3: Visualize Metrics and Logs

1. Go to **CloudWatch > Logs**: verify log streams from EC2
2. Go to **Metrics > CWAgent**: view CPU and memory metrics

---

## 🔔 Part 4: Create Alarm

1. Go to **CloudWatch > Alarms > Create Alarm**
2. Metric: `CPUUtilization` from EC2 or CWAgent
3. Threshold: `> 50%`
4. (Optional) Add SNS topic for email notification

---

## 🧪 Challenge

- Configure alert for available disk space < 10%
- Send logs to CloudWatch Logs from a custom file

---

## 🧹 Cleanup

- Delete the alarm
- Remove log groups
- Terminate EC2 instance

---

## ✅ Summary

You learned how to:
- Use the CloudWatch Agent to collect and visualize metrics and logs
- Set alarms for proactive system monitoring
- Monitor EC2 instances like a production-ready DevOps engineer

