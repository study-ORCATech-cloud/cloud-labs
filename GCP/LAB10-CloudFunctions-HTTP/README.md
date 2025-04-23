# GCP LAB10: Resource Overview – Cloud Functions and HTTP Triggers

## ⚡ What Resources Are We Working With?

In **GCP LAB10**, you’ll build a lightweight **serverless application** using **Cloud Functions** and expose it through an **HTTP trigger**. This is ideal for microservices, automation, and event-driven workflows.

---

## ☁️ Cloud Functions

### Description
Cloud Functions is a FaaS (Function-as-a-Service) product that runs single-purpose functions in response to events.

### Key Concepts
- **Trigger Type**: HTTP (for API-style endpoints)
- **Runtime**: Choose from Node.js, Python, Go, etc.
- **Region**: Function executes close to users (low latency)

### What You Do in the Lab
- Create a function triggered by HTTP requests
- Deploy simple handler to return JSON

---

## 🌐 HTTP Trigger

### Description
Exposes the function over a public URL that clients can call with GET/POST requests.

### What You Do in the Lab
- Enable anonymous access (or secure with IAM)
- Test via curl or browser

---

## 🔐 IAM Policy (Optional)

### Description
Define who can invoke the function

### What You Might Do
- Use `allUsers` for public access (demo only)
- Restrict to authenticated identities in production

---

## ✅ Summary

| Resource         | Purpose                                           | Created Using    |
|------------------|----------------------------------------------------|------------------|
| Cloud Function   | Serverless compute logic                         | Console & CLI    |
| HTTP Trigger     | Exposes function as a web endpoint                | Console & CLI    |
| IAM Policy       | Controls who can call the function                | Console          |

This lab equips you to create scalable, event-driven services on GCP using Cloud Functions and API-style integration.

