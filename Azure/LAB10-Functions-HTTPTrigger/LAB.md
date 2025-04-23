# LAB10: Build a Serverless API with Azure Functions and HTTP Trigger

## ⚡ Lab Overview

In this final Azure lab, you’ll create a **Function App** with an **HTTP trigger** that returns a JSON response. This is a lightweight, scalable way to deploy APIs or automation logic without maintaining servers.

---

## 🎯 Objectives

- Create a Function App using Python or JavaScript
- Configure an HTTP-triggered function
- Test endpoint via browser or curl

---

## 🛠️ Prerequisites

- Azure subscription
- Azure CLI or Portal access
- Basic coding familiarity (Python or JavaScript)

---

## 📦 Part 1: Create Function App

1. Go to **Function Apps > Create**
2. Name: `lab10-func`
3. Runtime: Python 3.10 or JavaScript
4. Hosting: Consumption (serverless)
5. Storage Account: Auto-created or choose existing

---

## 🔁 Part 2: Create HTTP Trigger Function

1. Inside the Function App > **Functions > Add**
2. Template: **HTTP trigger**
3. Name: `HttpHello`
4. Authorization Level: Anonymous (lab only)
5. Add the following code (Python):
```python
def main(req):
    return func.HttpResponse("{"message": "Hello from Azure Function!"}", mimetype="application/json")
```

---

## 🌐 Part 3: Test the Endpoint

1. Copy the function URL from the portal
2. Test via browser or terminal:
```bash
curl https://lab10-func.azurewebsites.net/api/HttpHello
```
Output:
```json
{"message": "Hello from Azure Function!"}
```

---

## 🧪 Challenge

- Modify the function to accept a query param (`name`) and return a personalized message
- Deploy a second function with POST method and JSON payload handling

---

## 🧹 Cleanup

- Delete the Function App and Storage Account

---

## ✅ Summary

You’ve successfully deployed a serverless API using Azure Functions — a fast and scalable solution for lightweight backend services, automation, and microservices architecture.

