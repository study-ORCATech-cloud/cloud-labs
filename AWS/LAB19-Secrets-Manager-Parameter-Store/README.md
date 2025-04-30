# AWS LAB19: Resource Overview – AWS Secrets Manager and Parameter Store

## 🗄️ What Resources Are We Working With?

In **AWS LAB19**, you'll explore **secure secrets and configuration management** using **AWS Secrets Manager and AWS Systems Manager Parameter Store**. This lab teaches you how to securely store, retrieve, rotate, and manage sensitive information like database credentials, API keys, and configuration parameters in a highly available and scalable manner.

---

## 📊 AWS Secrets Manager

### Description
AWS Secrets Manager is a service that helps you protect access to your applications, services, and IT resources by securely storing and managing secrets. It enables you to replace hardcoded credentials in your code with an API call to Secrets Manager, protecting these secrets from being compromised.

### Key Concepts
- **Secrets**: Encrypted pieces of information that you want to keep secure
- **Secret Rotation**: Automatic updating of secrets at specified intervals
- **Encryption**: AES-256 encryption through AWS KMS
- **Resource Policies**: Control access to specific secrets
- **Multi-Region Secrets**: Replicate secrets across AWS regions
- **Tags**: Add metadata to categorize and manage secrets
- **Versioning**: Track changes to secrets over time

### What You Do in the Lab
- Create and store different types of secrets
- Configure resource-based policies for access control
- Implement automatic rotation for database credentials
- Access secrets using AWS CLI and SDK
- Monitor secrets access using CloudTrail
- Create and use secrets in applications

---

## 🔎 AWS Systems Manager Parameter Store

### Description
AWS Systems Manager Parameter Store provides secure, hierarchical storage for configuration data management and secrets management. It can be used to store plaintext configuration data such as database strings or encrypted secret data like passwords and license keys.

### Key Concepts
- **Parameters**: Named values stored in a hierarchical structure
- **Parameter Tiers**: Standard (free) and Advanced (paid) with different limits
- **SecureString Parameters**: Encrypted parameters for sensitive data
- **Parameter Hierarchies**: Organize parameters using path-like naming
- **Parameter Versions**: Track changes to parameter values
- **Parameter Policies**: Define rules for parameter expiration and notification
- **Public Parameters**: AWS-provided parameters like latest AMIs

### What You Do in the Lab
- Create parameters of different types (String, StringList, SecureString)
- Build hierarchical parameter structures for organized management
- Use parameter versions for tracking changes
- Apply parameter policies for automatic expiration
- Access parameters using AWS CLI, SDK, and CloudFormation
- Implement a strategy for managing application configurations

---

## 🔄 Secrets Manager and Parameter Store Integration

### Description
Integrating Secrets Manager with Parameter Store provides a comprehensive solution for managing both sensitive secrets and application configuration data.

### Key Concepts
- **Complementary Services**: Using each service for its strengths
- **Cross-References**: Referencing Secrets Manager secrets in Parameter Store
- **Cost Optimization**: Balancing between free and paid services
- **Security Depth**: Implementing multiple layers of protection
- **Operational Efficiency**: Standardizing configuration management

### What You Do in the Lab
- Define strategies for which service to use when
- Set up cross-service references for unified management
- Build a complete secrets and configuration management solution
- Implement best practices for secure application development
- Create a sample application that uses both services

---

## ✅ Summary

| Resource               | Purpose                                             | Created Using    |
|------------------------|-----------------------------------------------------|------------------|
| Secrets Manager Secret | Store sensitive information like credentials        | Console & CLI    |
| Secret Rotation        | Automatically update credentials periodically       | Console & CLI    |
| Resource Policy        | Control access to specific secrets                  | Console & CLI    |
| Parameter Store Item   | Store configuration data and references             | Console & CLI    |
| Parameter Hierarchy    | Organize parameters in logical structure            | Console & CLI    |
| Parameter Policies     | Manage parameter expiration and notifications       | Console & CLI    |
| IAM Policies           | Control access to secrets and parameters            | Console & CLI    |

This lab provides hands-on experience with AWS Secrets Manager and Parameter Store, teaching you how to implement a secure, scalable solution for managing sensitive data and application configurations. These services are essential for modern secure application development and operational best practices. 