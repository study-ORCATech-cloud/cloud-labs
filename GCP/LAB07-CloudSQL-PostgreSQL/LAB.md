# LAB07: Deploy and Connect to Cloud SQL (PostgreSQL)

## 🗄️ Lab Overview

In this lab, you’ll deploy a managed **Cloud SQL PostgreSQL** instance, set up secure access, and connect using `psql` to execute queries. This simulates a real-world database deployment for cloud apps.

---

## 🎯 Objectives

- Provision a Cloud SQL PostgreSQL instance
- Enable network access securely
- Connect and interact with the database

---

## 🛠️ Prerequisites

- GCP project with billing enabled
- `psql` client or Cloud Shell access

---

## 🧱 Part 1: Create Cloud SQL Instance

1. Go to **SQL > Create Instance > PostgreSQL**
2. Instance ID: `lab07-sql`
3. Password: Set strong root password
4. Region: `us-central1`
5. Machine Type: `db-f1-micro` (for lab)
6. Enable backups

---

## 🔐 Part 2: Configure Access

1. Under **Connections > Networking**, click **Add Network**
2. Add your current IP or `0.0.0.0/0` for testing only
3. Save changes

---

## 🔌 Part 3: Connect Using Cloud Shell or psql

1. Open **Cloud Shell** and run:
```bash
gcloud sql connect lab07-sql --user=postgres
```
2. Once connected, run:
```sql
CREATE DATABASE demo;
\c demo
CREATE TABLE users (id INT, name TEXT);
INSERT INTO users VALUES (1, 'Cloud SQL');
SELECT * FROM users;
```

---

## 🧪 Challenge

- Enable automated failover (High Availability)
- Create a read replica in another region

---

## 🧹 Cleanup

- Delete the Cloud SQL instance to avoid charges

---

## ✅ Summary

You’ve created a managed PostgreSQL instance on GCP and learned how to configure access, run queries, and apply best practices for availability and scalability.