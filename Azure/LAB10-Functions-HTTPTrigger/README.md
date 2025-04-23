# Azure LAB10: Resource Overview – Azure Functions and HTTP Trigger

## ⚡ What Resources Are We Working With?

In **Azure LAB10**, you'll explore **serverless computing** using **Azure Functions** triggered via HTTP requests. You’ll create a function app, write a small HTTP-triggered function, and test it via browser or curl.

---

## 🔁 Azure Function App

### Description
Function Apps host individual functions and provide the environment for execution.

### Key Concepts
- **Runtime Stack**: Choose a language like Python, C#, or JavaScript
- **Hosting Plan**: Consumption (pay-per-execution) or App Service Plan
- **Storage Account**: Required to manage state and logs

### What You Do in the Lab
- Create a Function App with Python or JavaScript runtime
- Deploy and test a basic function

---

## 🌐 HTTP Trigger

### Description
The trigger defines how the function is invoked — in this lab, via a public HTTP endpoint.

### What You Do in the Lab
- Add an HTTP trigger to the function
- Respond to GET requests with a JSON response
- Test endpoint using browser or Postman

---

## 🔐 Authentication and Authorization (Optional)

### Description
Function endpoints can be configured as anonymous, function-key, or Azure AD secured.

### What You Might Do
- Leave anonymous for demo/testing
- (Optional) Add function key for basic access control

---

## ✅ Summary

| Resource            | Purpose                                        | Created Using    |
|---------------------|------------------------------------------------|------------------|
| Azure Function App  | Executes lightweight serverless code           | Portal & CLI     |
| HTTP Trigger        | Invokes function via browser or client         | Portal & CLI     |
| Storage Account     | Stores function metadata and logs              | Auto-provisioned |
| Authentication (opt)| Controls endpoint access                       | Portal           |

This lab introduces you to scalable, event-driven compute for building modern APIs and automation workflows in Azure.

