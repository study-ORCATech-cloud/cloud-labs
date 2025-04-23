# LAB10: Deploy a Serverless HTTP API with Cloud Functions

## ⚡ Lab Overview

In this final GCP lab, you’ll create a **Cloud Function** with an **HTTP trigger** that responds to requests. This serverless approach is ideal for microservices, APIs, and automation workflows.

---

## 🎯 Objectives

- Write and deploy a Cloud Function using HTTP trigger
- Secure and test the endpoint
- Expand to handle query parameters

---

## 🛠️ Prerequisites

- GCP project with billing enabled
- Cloud Functions and Cloud Build APIs enabled

---

## 📋 Part 1: Create the Function

1. Go to **Cloud Functions > Create Function**
2. Name: `helloHttp`
3. Region: `us-central1`
4. Trigger: **HTTP** (Allow unauthenticated invocations)
5. Runtime: Python 3.10 or Node.js 18

### Sample Python Code:
```python
def hello_http(request):
    name = request.args.get('name', 'world')
    return f'Hello, {name}!'
```

---

## 🌐 Part 2: Test the Function

1. Copy the function URL after deployment
2. Access in browser or use curl:
```bash
curl "https://REGION-PROJECT.cloudfunctions.net/helloHttp?name=DevOps"
```
Expected Output:
```
Hello, DevOps!
```

---

## 🔐 Part 3: Optional IAM Restriction

1. Go to **Cloud Functions > Permissions**
2. Remove `allUsers` from **Invoker role**
3. Add only trusted IAM users or groups

---

## 🧪 Challenge

- Modify the function to return JSON
- Accept POST request and extract data from JSON body

---

## 🧹 Cleanup

- Delete the function to avoid charges

---

## ✅ Summary

You’ve built and tested a serverless function with an HTTP trigger in GCP. This skill is critical for scalable backend logic, automation, and cloud-native APIs.

