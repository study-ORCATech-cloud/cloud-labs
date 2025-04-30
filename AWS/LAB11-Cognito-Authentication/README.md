# AWS LAB11: Resource Overview – Amazon Cognito Authentication

## 🔐 What Resources Are We Working With?

In **AWS LAB11**, you'll explore **user authentication and authorization** using **Amazon Cognito**. This lab teaches you how to implement secure user sign-up, sign-in, and access control for web and mobile applications without building and maintaining your own authentication system.

---

## 👥 Amazon Cognito User Pools

### Description
User pools are user directories that provide sign-up and sign-in options for your app users. They handle the entire authentication workflow and provide JSON Web Tokens (JWTs) upon successful authentication.

### Key Concepts
- **User Pool**: A user directory with customizable sign-up/sign-in flows
- **App Client**: Configuration for your application to interact with the user pool
- **Attributes**: User information fields (required and custom)
- **Authentication Flow**: Password-based, MFA, custom challenges

### What You Do in the Lab
- Create a user pool with required attributes
- Configure app client settings
- Test user sign-up and sign-in
- Examine authentication tokens (ID, Access, Refresh)

---

## 🔄 Amazon Cognito Identity Pools

### Description
Identity pools (federated identities) provide temporary AWS credentials to access other AWS services directly from your application.

### Key Concepts
- **Identity Pool**: Provides temporary, limited-privilege AWS credentials
- **Authentication Providers**: User pools, social identity providers, SAML
- **IAM Roles**: Control what AWS resources authenticated users can access

### What You Do in the Lab
- Create an identity pool linked to your user pool
- Configure IAM roles for authenticated and unauthenticated users
- Test obtaining temporary credentials

---

## 🔌 Amazon Cognito Hosted UI

### Description
A customizable, ready-to-use web UI for user sign-up and sign-in provided by Amazon Cognito.

### What You Do in the Lab
- Configure the hosted UI domain
- Customize the appearance
- Set up redirect URIs for authentication flow
- Test the hosted UI sign-up and sign-in process

---

## 🛡️ IAM Roles for Cognito

### Description
IAM roles define what AWS services users can access after authentication through Cognito.

### What You Do in the Lab
- Create authenticated and unauthenticated IAM roles
- Configure appropriate permissions policies
- Attach roles to your Cognito identity pool

---

## ✅ Summary

| Resource                | Purpose                                               | Created Using    |
|-------------------------|-------------------------------------------------------|------------------|
| Cognito User Pool       | User directory with authentication workflows          | Console & CLI    |
| Cognito App Client      | Configuration for application integration             | Console & CLI    |
| Cognito Identity Pool   | Provides temporary AWS credentials                    | Console & CLI    |
| Cognito Hosted UI       | Ready-to-use authentication interface                 | Console & CLI    |
| IAM Roles               | Define permissions for authenticated users            | Console & CLI    |

This lab provides a comprehensive introduction to implementing secure, scalable authentication for your applications using Amazon Cognito, following best practices for modern identity management. 