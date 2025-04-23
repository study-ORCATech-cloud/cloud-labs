# Azure LAB06: Resource Overview – Azure Monitor and Log Analytics

## 📊 What Resources Are We Working With?

In **Azure LAB06**, you’ll implement observability using **Azure Monitor**, **Log Analytics**, and **Metrics**. These tools enable you to visualize, analyze, and respond to events across your infrastructure and applications.

---

## 🧠 Azure Monitor

### Description
A platform service that provides full-stack monitoring for cloud and on-prem resources.

### Key Concepts
- **Metrics**: Numeric performance data (CPU, memory)
- **Alerts**: Notifications based on metric thresholds
- **Insights**: Dashboards for specific services like VMs, containers

### What You Do in the Lab
- Access built-in insights for VMs or services
- View metrics in charts

---

## 📦 Log Analytics Workspace

### Description
A central data store for logs collected from Azure resources.

### Key Concepts
- **Kusto Query Language (KQL)**: Used to analyze logs
- **Retention Period**: Duration logs are stored
- **Data Sources**: VMs, diagnostics settings, services

### What You Do in the Lab
- Create a Log Analytics Workspace
- Connect it to a VM
- Query logs using KQL

---

## 🔔 Alerts

### Description
Alerts notify you when monitored conditions are met.

### What You Do in the Lab
- Create alert rule for CPU threshold
- Link to action group (email or webhook)

---

## ✅ Summary

| Resource               | Purpose                                          | Created Using    |
|------------------------|--------------------------------------------------|------------------|
| Azure Monitor          | View metrics and platform-wide observability     | Portal & CLI     |
| Log Analytics Workspace| Collect and query structured log data            | Portal & CLI     |
| Alerts & Action Groups | Notify admins or trigger automation              | Portal & CLI     |

This lab builds foundational skills in cloud monitoring, empowering proactive troubleshooting and performance optimization.

