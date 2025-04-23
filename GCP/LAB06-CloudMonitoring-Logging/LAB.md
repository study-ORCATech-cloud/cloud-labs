# LAB06: Monitor VMs with Cloud Monitoring and Logging

## 📊 Lab Overview

In this lab, you’ll configure **Cloud Monitoring** and **Cloud Logging** to gain visibility into a Compute Engine VM. You’ll create custom dashboards, alerts, and explore logs for diagnostic insights.

---

## 🎯 Objectives

- Enable monitoring and logging for a VM
- Create a dashboard with metrics
- Set up an alert policy
- Filter and explore logs

---

## 🛠️ Prerequisites

- GCP account with editor access
- At least one VM instance deployed

---

## 🧱 Part 1: Enable Cloud Monitoring and Logging

1. Go to **Monitoring > Overview > Enable API**
2. Visit **Logging > Log Explorer** to confirm logs are being collected
3. Ensure VM has the **Monitoring and Logging agent** (installed by default on many images)

---

## 📊 Part 2: Create Dashboard and View Metrics

1. Go to **Monitoring > Dashboards > Create Dashboard**
2. Add widgets:
   - Line chart: CPU usage (metric: `compute.googleapis.com/instance/cpu/utilization`)
   - Gauge: Memory usage (metric varies by OS)

---

## 🔔 Part 3: Create an Alerting Policy

1. Go to **Monitoring > Alerting > Create Policy**
2. Condition: VM CPU > 70% for 5 minutes
3. Action: Add an email notification channel

---

## 🪵 Part 4: Explore Logs

1. Go to **Logging > Log Explorer**
2. Query logs using filters:
```text
resource.type="gce_instance"
logName="projects/<project-id>/logs/syslog"
```
3. Save queries and export logs (optional)

---

## 🧪 Challenge

- Add uptime check to monitor public IP
- Export logs to BigQuery for analysis

---

## 🧹 Cleanup

- Delete alerting policy, dashboards, and VM if not needed

---

## ✅ Summary

You've now set up a monitoring system using Cloud-native tools in GCP, providing observability and proactive alerting for your applications.