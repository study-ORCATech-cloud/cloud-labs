# LAB06: Monitor VMs with Azure Monitor and Log Analytics

## 📊 Lab Overview

In this lab, you will collect and analyze metrics and logs using **Azure Monitor** and **Log Analytics**. You’ll enable monitoring on a VM, query collected data using **Kusto Query Language (KQL)**, and create alerts for proactive management.

---

## 🎯 Objectives

- Set up Log Analytics workspace
- Enable diagnostics for a VM
- Query performance logs with KQL
- Create metric alerts for CPU usage

---

## 🛠️ Prerequisites

- Azure account with contributor rights
- A running Azure VM (Linux or Windows)

---

## 🧱 Part 1: Create Log Analytics Workspace

1. Go to **Monitor > Logs > Create Workspace**
2. Name: `lab06-logs`
3. Region: Same as your VM

---

## 📦 Part 2: Connect VM to Workspace

1. Go to **Virtual Machines > lab06-vm > Monitoring > Logs**
2. Enable diagnostics and connect to `lab06-logs`
3. Enable collection of:
   - Performance counters (CPU, memory)
   - Syslog (Linux) or Event Logs (Windows)

---

## 🔍 Part 3: Run KQL Queries

1. Go to **Monitor > Logs**
2. Run:
```kusto
Perf
| where ObjectName == "Processor" and CounterName == "% Processor Time"
| summarize avg(CounterValue) by bin(TimeGenerated, 5m)
```
3. Save query and pin to dashboard (optional)

---

## 🔔 Part 4: Create an Alert Rule

1. Go to **Monitor > Alerts > New Alert Rule**
2. Scope: VM resource
3. Condition: CPU > 70% for 5 min
4. Action: Email via Action Group
5. Name and create the alert

---

## 🧪 Challenge

- Use `Heartbeat` and `Syslog` logs for basic audit checks
- Set alerts for disk or memory usage

---

## 🧹 Cleanup

- Delete the alert rule, workspace, and VM

---

## ✅ Summary

You've successfully monitored a VM using Azure Monitor and Log Analytics—tools essential for visibility, performance optimization, and automated issue detection in cloud environments.

