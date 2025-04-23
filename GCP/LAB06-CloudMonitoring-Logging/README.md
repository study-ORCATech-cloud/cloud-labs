# GCP LAB06: Resource Overview – Cloud Monitoring and Logging

## 📊 What Resources Are We Working With?

In **GCP LAB06**, you’ll implement observability for virtual machines using **Cloud Monitoring**, **Logging**, and **Metrics Explorer**. You'll also create alerts based on thresholds like CPU utilization.

---

## 📈 Cloud Monitoring

### Description
A built-in GCP tool that collects and visualizes performance metrics across resources.

### Key Concepts
- **Dashboards**: Custom views to track key metrics
- **Uptime Checks**: Verify availability of services
- **Alerts**: Notify based on metric thresholds

### What You Do in the Lab
- View CPU and memory usage
- Create a dashboard for a VM
- Create alerts for CPU spikes

---

## 🪵 Cloud Logging

### Description
Centralizes logs from VMs, Kubernetes, Cloud Functions, and more.

### Key Concepts
- **Log-based Metrics**: Convert log queries into chartable metrics
- **Export Logs**: To BigQuery, Cloud Storage, or Pub/Sub

### What You Do in the Lab
- Explore logs from a running VM
- Use filters to find SSH access or startup logs

---

## 🔔 Alerting Policies

### Description
Policies that define conditions to send alerts via email, SMS, or Slack.

### What You Do in the Lab
- Create policy to alert on CPU > 60%
- Link to notification channel (email)

---

## ✅ Summary

| Resource             | Purpose                                          | Created Using    |
|----------------------|--------------------------------------------------|------------------|
| Cloud Monitoring     | Tracks metrics and builds dashboards             | Console & CLI    |
| Cloud Logging        | Centralizes and searches logs                    | Console & CLI    |
| Alerting Policy      | Sends notifications based on conditions          | Console          |

This lab equips you to monitor and troubleshoot GCP workloads proactively, ensuring reliability and performance.

