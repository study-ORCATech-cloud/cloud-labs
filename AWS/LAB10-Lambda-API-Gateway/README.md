# AWS LAB10: Resource Overview – AWS Lambda and API Gateway

## ⚡ What Resources Are We Working With?

In **AWS LAB10**, you’ll explore **serverless computing** using **AWS Lambda** and **Amazon API Gateway**. This lab teaches you how to deploy a simple function that executes code in response to HTTP requests — without provisioning or managing servers.

---

## 🧠 AWS Lambda

### Description
Lambda lets you run code without provisioning or managing servers. You only pay for the compute time you consume.

### Key Concepts
- **Function**: A block of code triggered by events (e.g., HTTP request, S3 upload)
- **Runtime**: Supported languages like Python, Node.js, Java, etc.
- **Execution Role**: IAM role that grants permissions to resources the function accesses

### What You Do in the Lab
- Write a simple Lambda function (e.g., return JSON response)
- Define an IAM role for function execution
- Deploy the function using Console or CLI

---

## 🌐 Amazon API Gateway

### Description
API Gateway provides a front door to your Lambda function, turning it into a RESTful HTTP endpoint.

### Key Concepts
- **API**: Collection of REST resources and methods
- **Resource**: URL path (e.g., `/hello`)
- **Method**: HTTP verb (GET, POST, etc.) mapped to a Lambda function

### What You Do in the Lab
- Create a REST API with one or more resources
- Link resources to your Lambda function
- Deploy the API to a stage (e.g., `dev`)
- Test the endpoint in a browser or with curl

---

## 🛡️ IAM Role (Execution Role)

### Description
Lambda needs permissions to run and access other AWS services.

### What You Do in the Lab
- Create a basic execution role with `AWSLambdaBasicExecutionRole`
- Attach the role to your Lambda function

---

## ✅ Summary

| Resource         | Purpose                                             | Created Using    |
|------------------|------------------------------------------------------|------------------|
| Lambda Function  | Runs code without managing infrastructure           | Console & CLI    |
| API Gateway      | Creates RESTful HTTP endpoints for functions        | Console & CLI    |
| IAM Role         | Grants Lambda permission to log and access services | Console & CLI    |

This lab gives you a full end-to-end introduction to serverless architecture using AWS-native tools. It's perfect for building lightweight APIs and event-driven automation workflows.

