# AWS LAB06: Resource Overview – CloudWatch Logs and Metrics

## 📊 What Resources Are We Working With?

In **AWS LAB06**, you'll explore **Amazon CloudWatch**, the monitoring and observability service used to track resource utilization, performance, and operational health. This lab teaches you how to collect logs and visualize metrics from EC2 instances.

---

## 📉 Amazon CloudWatch Metrics

### Description
CloudWatch automatically collects metrics from AWS services (CPU, disk, network, etc.) and enables custom metrics.

### Key Concepts
- **Namespace**: Logical grouping of metrics
- **Dimension**: Key-value pair for filtering metrics (e.g., `InstanceId`)
- **Alarms**: Triggered when metric thresholds are crossed

### What You Do in the Lab
- View built-in metrics for EC2
- Create a custom CloudWatch Alarm for CPU usage

---

## 🪵 Amazon CloudWatch Logs

### Description
CloudWatch Logs collect and store logs from EC2 instances, applications, and services.

### Key Concepts
- **Log Group**: Container for logs with similar purpose
- **Log Stream**: Sequence of log events from a source (e.g., one EC2 instance)
- **Retention**: How long to keep log data

### What You Do in the Lab
- Install CloudWatch Agent on an EC2 instance
- Configure it to send logs to CloudWatch Logs
- Explore log groups and retention settings

---

## ⚙️ CloudWatch Agent

### Description
The agent collects system-level metrics and logs from EC2 and pushes them to CloudWatch.

### What You Do in the Lab
- Download and install the agent
- Use the wizard or config file to start log and metric collection

---

## 🔔 CloudWatch Alarms

### Description
Alarms monitor metric thresholds and send notifications via SNS or take automatic actions.

### What You Do in the Lab
- Create a basic alarm for CPU usage on an EC2 instance
- (Optional) Integrate with SNS to get email alerts

---

## ✅ Summary

| Resource             | Purpose                                      | Created Using    |
|----------------------|-----------------------------------------------|------------------|
| CloudWatch Metrics   | Monitors performance data (CPU, disk, etc.)  | Console & CLI    |
| CloudWatch Logs      | Stores logs from apps and OS                 | Console & CLI    |
| CloudWatch Agent     | Sends custom metrics and logs from EC2       | Installed manually|
| CloudWatch Alarms    | Triggers actions based on metric thresholds | Console & CLI    |

Monitoring is critical for both operational awareness and automated incident response. This lab sets the foundation for creating observable systems.