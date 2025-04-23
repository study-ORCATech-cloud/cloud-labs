# LAB10: Deploy a Serverless Function with API Gateway and Lambda (Console & CLI)

## ⚡ Lab Overview

In this final AWS lab, you'll build and expose a **serverless REST API** using **AWS Lambda** and **Amazon API Gateway**. You’ll write a basic function that returns a JSON response and connect it to an HTTP endpoint with API Gateway.

---

## 🎯 Objectives

- Create a Lambda function using the AWS Console
- Write and test a Python-based handler
- Expose the function using API Gateway
- Test the REST endpoint via browser or `curl`

---

## 🛠️ Prerequisites

- AWS account
- IAM permissions for Lambda and API Gateway
- AWS CLI configured (optional for CLI parts)

---

## 🧠 Part 1: Create Lambda Function

1. Go to **Lambda > Create Function**
2. Author from scratch
   - Name: `lab10-hello`
   - Runtime: Python 3.10
   - Role: Create new role with basic Lambda permissions

### Add code:
```python
def lambda_handler(event, context):
    return {
        'statusCode': 200,
        'headers': {'Content-Type': 'application/json'},
        'body': '{"message": "Hello from Lambda!"}'
    }
```

3. Click **Deploy** and test with a sample event

---

## 🌐 Part 2: Create API Gateway Endpoint

1. Go to **API Gateway > Create API**
2. Choose **HTTP API** > Build
3. Integration: Lambda Function > Select `lab10-hello`
4. Route: `GET /hello`
5. Stage name: `dev`
6. Click **Deploy**

### Test the endpoint
```bash
curl https://<api-id>.execute-api.<region>.amazonaws.com/dev/hello
```
Should return:
```json
{"message": "Hello from Lambda!"}
```

---

## 💻 Optional CLI (Create Lambda)
```bash
zip function.zip lambda_function.py
aws lambda create-function \
  --function-name lab10-hello-cli \
  --runtime python3.10 \
  --role <execution-role-arn> \
  --handler lambda_function.lambda_handler \
  --zip-file fileb://function.zip
```

---

## 🧪 Challenge

- Modify the function to accept a name parameter and return a personalized message
- Add a POST route with input validation

---

## 🧹 Cleanup

- Delete the Lambda function and API Gateway

---

## ✅ Summary

You've deployed a fully functional serverless API using AWS-native services. This architecture reduces operational overhead and is perfect for modern, event-driven applications.