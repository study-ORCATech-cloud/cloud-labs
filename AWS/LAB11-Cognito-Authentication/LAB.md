# LAB11: Implement User Authentication with Amazon Cognito (Console & CLI)

## ⚡ Lab Overview

In this advanced lab, you'll implement **secure user authentication** for web and mobile applications using **Amazon Cognito**. You'll create a complete authentication system with user sign-up, sign-in, and identity management capabilities. Additionally, you'll learn how to use Cognito to grant authenticated users secure access to other AWS services. This serverless authentication service is a fundamental building block for modern application architectures that require secure, scalable identity management.

### 💼 Real-World DevOps Applications

Authentication and authorization are critical components of nearly every production application. Amazon Cognito offers several key advantages:

- **Security Best Practices**: Implements industry standards like OAuth 2.0, OpenID Connect, and SAML
- **Scalability**: Handles millions of users without infrastructure management
- **Compliance**: Helps meet security requirements for regulated industries
- **Customizability**: Supports custom authentication flows and user attribute validation
- **Multi-Factor Authentication**: Adds an additional security layer for sensitive applications

Common production use cases include:

- **Web/Mobile App Authentication**: User registration and login for applications
- **API Authorization**: Secure access to backend APIs using JWT tokens
- **Single Sign-On (SSO)**: Integration with enterprise identity providers
- **Social Identity Federation**: Login with Google, Facebook, Amazon, and other providers
- **B2B Application Access**: Partner access management with controlled permissions

---

## 🎯 Objectives

By the end of this lab, you will be able to:
- Create and configure a Cognito User Pool using the AWS Console and CLI
- Set up App Clients for application integration
- Implement and test user sign-up and sign-in flows
- Configure the Cognito Hosted UI for ready-to-use authentication
- Create a Cognito Identity Pool for AWS service access
- Set up appropriate IAM roles for authenticated and unauthenticated users
- Understand JWT tokens and how they secure application access
- Implement best practices for authentication in cloud applications

---

## 🛠️ Prerequisites

- AWS account with administrative permissions
- Basic understanding of authentication concepts and JWT tokens
- Familiarity with IAM roles and policies
- AWS CLI installed and configured (refer to [CLI Setup Guide](../../CLI-Setup.md))
- A web browser for testing the Cognito Hosted UI

---

# 🌐 PART 1: AWS MANAGEMENT CONSOLE IMPLEMENTATION

In this first part of the lab, you'll create and configure Amazon Cognito resources using the AWS Management Console interface.

## 👥 Step 1: Create a Cognito User Pool (Console)

1. Sign in to the AWS Management Console
2. Navigate to the **Amazon Cognito** service
3. Click **Create user pool**
4. For **Cognito user pool sign-in options**:
   - Select **Email**
   - Click **Next**
5. For **Password policy**:
   - Keep the **Cognito defaults** option selected
   - Click **Next**
6. For **Multi-factor authentication**:
   - Select **No MFA** (for simplicity in this lab)
   - Click **Next**
7. For **Sign-up experience**:
   - Under **Required attributes**, verify that **email** is selected
   - Under **Verifications**, keep **Verify email address** selected
   - Click **Next**
8. For **Message delivery**:
   - Keep **Send email with Cognito** selected
   - Click **Next**
9. For **Integrate your app**:
   - **User pool name**: `lab11-user-pool`
   - Under **Hosted authentication pages**:
      - Check **Use the Cognito Hosted UI**
      - **Domain type**: Choose **Use a Cognito domain**
      - **Cognito domain prefix**: Enter a unique prefix like `lab11-yourusername` (must be globally unique)
   - Under **Initial app client**:
      - **App type**: Select **Public client**
      - **App client name**: `lab11-app-client`
      - **Client secret**: Select **Don't generate a client secret**
   - Under **Allowed callback URLs**:
      - Add `https://example.com/callback`
      - Add `https://oauth.pstmn.io/v1/callback` (for testing with Postman)
   - Under **Advanced app client settings**:
      - For **Authentication flows**:
         - Ensure **ALLOW_USER_PASSWORD_AUTH** is selected
      - For **OAuth 2.0 grant types**:
         - Ensure **Authorization code grant** and **Implicit grant** are selected
      - For **OpenID Connect scopes**:
         - Select **Email**, **OpenID**, and **Profile**
   - Click **Next**
10. Review all your settings and click **Create user pool**

## 📝 Step 2: Note Important Information

After creation, take note of the following information (you'll need it later):

1. **User Pool ID**: Located in the **User pool overview** section
2. **App client ID**: Navigate to **App integration** → **App clients and analytics** and note the ID
3. **Domain URL**: Navigate to **App integration** → **Domain** and note the full domain URL

## 🔌 Step 3: Configure and Test the Hosted UI (Console)

### Access the Hosted UI

1. Construct the Hosted UI URL using the following format:
```
https://[your-domain-prefix].auth.eu-west-1.amazoncognito.com/login?client_id=[app-client-id]&response_type=code&scope=email+openid+profile&redirect_uri=https://example.com/callback
```

2. Replace `[your-domain-prefix]` with the domain prefix you created
3. Replace `[app-client-id]` with your App Client ID

### Test User Sign-Up

1. Access the Hosted UI URL in your browser
2. Click **Sign up** to create a new account
3. Fill in the required information:
   - Email
   - Password (meeting complexity requirements)
4. Click **Sign up**
5. You'll receive a verification code at your email address
6. Enter the verification code to confirm your account

### Test User Sign-In

1. Return to the Hosted UI
2. Enter the email and password you just created
3. Click **Sign in**
4. You should be redirected to the callback URL with an authorization code
   - Since we're using example.com, you'll see an error page, but check the URL for the code parameter

## 🔄 Step 4: Create and Configure an Identity Pool (Console)

### Create an Identity Pool

1. Navigate to the **Amazon Cognito** service
2. Choose **Federated Identities**
3. Click **Create new identity pool**
4. For basic information:
   - **Identity pool name**: `lab11-identity-pool`
   - Under **Authentication providers**:
      - Select the **Cognito** tab
      - Enter your **User Pool ID**
      - Enter your **App Client ID**
   - Click **Create Pool**
5. For IAM roles:
   - For **Authenticated role**:
      - Select **Create a new IAM Role**
      - Enter the name `lab11-cognito-authenticated-role`
   - For **Unauthenticated role**:
      - Select **Create a new IAM Role**
      - Enter the name `lab11-cognito-unauthenticated-role`
   - Click **Allow**

### Configure IAM Roles

1. Navigate to the **IAM** service
2. Select **Roles** from the left menu
3. Search for and select `lab11-cognito-authenticated-role`
4. Click **Add permissions** → **Attach policies**
5. Search for and select `AmazonS3ReadOnlyAccess` as an example permission
6. Click **Add permissions**

## 🧹 Step 5: Console Implementation Cleanup

To avoid ongoing charges, clean up the resources created in the console part of this lab:

1. Navigate to **Cognito** → **User Pools**
2. Select your user pool and click **Delete**
3. Enter `delete` to confirm and click **Delete user pool**
4. Navigate to **Cognito** → **Federated Identities**
5. Select your identity pool and click **Delete identity pool**
6. Check the confirmation box and click **Delete pool**
7. Navigate to **IAM** → **Roles**
8. Select `lab11-cognito-authenticated-role` and click **Delete**
9. Enter the role name to confirm and click **Delete**
10. Repeat steps 8-9 for `lab11-cognito-unauthenticated-role`

---

# 💻 PART 2: AWS CLI IMPLEMENTATION

In this second part of the lab, you'll create the same Cognito resources using the AWS Command Line Interface (CLI).

## ✨ Step 1: Create a Cognito User Pool (CLI)

```bash
# Create a User Pool
aws cognito-idp create-user-pool \
  --pool-name lab11-user-pool \
  --auto-verified-attributes email \
  --schema Name=email,Required=true \
  --policies '{"PasswordPolicy":{"MinimumLength":8,"RequireUppercase":true,"RequireLowercase":true,"RequireNumbers":true,"RequireSymbols":true}}' \
  --region eu-west-1

# Store the User Pool ID for subsequent commands
USER_POOL_ID=$(aws cognito-idp list-user-pools --max-results 20 --region eu-west-1 | jq -r '.UserPools[] | select(.Name=="lab11-user-pool") | .Id')
echo "User Pool ID: $USER_POOL_ID"

# Create an App Client
aws cognito-idp create-user-pool-client \
  --user-pool-id $USER_POOL_ID \
  --client-name lab11-app-client \
  --no-generate-secret \
  --explicit-auth-flows ALLOW_USER_PASSWORD_AUTH ALLOW_REFRESH_TOKEN_AUTH \
  --allowed-o-auth-flows implicit code \
  --allowed-o-auth-scopes openid email profile \
  --callback-urls https://example.com/callback https://oauth.pstmn.io/v1/callback \
  --region eu-west-1

# Store the App Client ID
APP_CLIENT_ID=$(aws cognito-idp list-user-pool-clients --user-pool-id $USER_POOL_ID --region eu-west-1 | jq -r '.UserPoolClients[0].ClientId')
echo "App Client ID: $APP_CLIENT_ID"

# Create a Domain
aws cognito-idp create-user-pool-domain \
  --domain lab11-yourusername \
  --user-pool-id $USER_POOL_ID \
  --region eu-west-1
```

Note: Replace `lab11-yourusername` with a unique domain prefix.

## 👤 Step 2: Manage Users with CLI

```bash
# Create a user
aws cognito-idp admin-create-user \
  --user-pool-id $USER_POOL_ID \
  --username user@example.com \
  --temporary-password Temp123! \
  --user-attributes Name=email,Value=user@example.com Name=email_verified,Value=true \
  --region eu-west-1

# List users in the pool
aws cognito-idp list-users \
  --user-pool-id $USER_POOL_ID \
  --region eu-west-1

# Set a permanent password (simulating first login password change)
aws cognito-idp admin-set-user-password \
  --user-pool-id $USER_POOL_ID \
  --username user@example.com \
  --password YourStrongPassword123! \
  --permanent \
  --region eu-west-1

# Test authentication (get JWT tokens)
aws cognito-idp initiate-auth \
  --auth-flow USER_PASSWORD_AUTH \
  --client-id $APP_CLIENT_ID \
  --auth-parameters USERNAME=user@example.com,PASSWORD=YourStrongPassword123! \
  --region eu-west-1
```

## 🔐 Step 3: Create and Configure an Identity Pool (CLI)

```bash
# Create an Identity Pool
aws cognito-identity create-identity-pool \
  --identity-pool-name lab11-identity-pool \
  --allow-unauthenticated-identities \
  --cognito-identity-providers ProviderName=cognito-idp.eu-west-1.amazonaws.com/$USER_POOL_ID,ClientId=$APP_CLIENT_ID,ServerSideTokenCheck=false \
  --region eu-west-1

# Store the Identity Pool ID
IDENTITY_POOL_ID=$(aws cognito-identity list-identity-pools --max-results 10 --region eu-west-1 | jq -r '.IdentityPools[] | select(.IdentityPoolName=="lab11-identity-pool") | .IdentityPoolId')
echo "Identity Pool ID: $IDENTITY_POOL_ID"

# Create trust policy documents
cat > trust-policy-auth.json << EOL
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Federated": "cognito-identity.amazonaws.com"
      },
      "Action": "sts:AssumeRoleWithWebIdentity",
      "Condition": {
        "StringEquals": {
          "cognito-identity.amazonaws.com:aud": "$IDENTITY_POOL_ID"
        },
        "ForAnyValue:StringLike": {
          "cognito-identity.amazonaws.com:amr": "authenticated"
        }
      }
    }
  ]
}
EOL

cat > trust-policy-unauth.json << EOL
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Federated": "cognito-identity.amazonaws.com"
      },
      "Action": "sts:AssumeRoleWithWebIdentity",
      "Condition": {
        "StringEquals": {
          "cognito-identity.amazonaws.com:aud": "$IDENTITY_POOL_ID"
        },
        "ForAnyValue:StringLike": {
          "cognito-identity.amazonaws.com:amr": "unauthenticated"
        }
      }
    }
  ]
}
EOL

# Create the IAM roles
aws iam create-role \
  --role-name lab11-cognito-authenticated-role \
  --assume-role-policy-document file://trust-policy-auth.json \
  --region eu-west-1

aws iam create-role \
  --role-name lab11-cognito-unauthenticated-role \
  --assume-role-policy-document file://trust-policy-unauth.json \
  --region eu-west-1

# Attach policies to the roles
aws iam attach-role-policy \
  --role-name lab11-cognito-authenticated-role \
  --policy-arn arn:aws:iam::aws:policy/AmazonS3ReadOnlyAccess \
  --region eu-west-1

aws iam attach-role-policy \
  --role-name lab11-cognito-unauthenticated-role \
  --policy-arn arn:aws:iam::aws:policy/AmazonS3ReadOnlyAccess \
  --region eu-west-1

# Set the IAM roles for the Identity Pool
aws cognito-identity set-identity-pool-roles \
  --identity-pool-id $IDENTITY_POOL_ID \
  --roles authenticated=arn:aws:iam::$(aws sts get-caller-identity --query Account --output text):role/lab11-cognito-authenticated-role,unauthenticated=arn:aws:iam::$(aws sts get-caller-identity --query Account --output text):role/lab11-cognito-unauthenticated-role \
  --region eu-west-1
```

## 🔍 Step 4: Test the Complete Authentication Flow (CLI)

```bash
# Exchange authorization code for tokens
aws cognito-idp admin-initiate-auth \
  --user-pool-id $USER_POOL_ID \
  --client-id $APP_CLIENT_ID \
  --auth-flow ADMIN_USER_PASSWORD_AUTH \
  --auth-parameters USERNAME=user@example.com,PASSWORD=YourStrongPassword123! \
  --region eu-west-1

# This will return ID, Access, and Refresh tokens
# Save the ID token for the next step (replace with your actual token)
ID_TOKEN="your-id-token-from-previous-command"

# Use ID token to get temporary AWS credentials
aws cognito-identity get-id \
  --identity-pool-id $IDENTITY_POOL_ID \
  --logins cognito-idp.eu-west-1.amazonaws.com/$USER_POOL_ID=$ID_TOKEN \
  --region eu-west-1

# This will return an Identity ID (replace with actual value)
IDENTITY_ID="your-identity-id-from-previous-command"

# Get temporary credentials
aws cognito-identity get-credentials-for-identity \
  --identity-id $IDENTITY_ID \
  --logins cognito-idp.eu-west-1.amazonaws.com/$USER_POOL_ID=$ID_TOKEN \
  --region eu-west-1

# This will return temporary AWS credentials (AccessKeyId, SecretKey, SessionToken)
# Configure a new profile with the temporary credentials
aws configure set aws_access_key_id "AccessKeyId-from-previous-step" --profile temp-cognito
aws configure set aws_secret_access_key "SecretKey-from-previous-step" --profile temp-cognito
aws configure set aws_session_token "SessionToken-from-previous-step" --profile temp-cognito
aws configure set region eu-west-1 --profile temp-cognito

# Test S3 access with the temporary credentials
aws s3 ls --profile temp-cognito
```

## 🧹 Step 5: CLI Implementation Cleanup

To avoid ongoing charges, clean up the resources created in the CLI part of this lab:

```bash
# Delete the user pool domain
aws cognito-idp delete-user-pool-domain \
  --domain lab11-yourusername \
  --user-pool-id $USER_POOL_ID \
  --region eu-west-1

# Delete the user pool
aws cognito-idp delete-user-pool \
  --user-pool-id $USER_POOL_ID \
  --region eu-west-1

# Delete the identity pool
aws cognito-identity delete-identity-pool \
  --identity-pool-id $IDENTITY_POOL_ID \
  --region eu-west-1

# Detach policies from IAM roles
aws iam detach-role-policy \
  --role-name lab11-cognito-authenticated-role \
  --policy-arn arn:aws:iam::aws:policy/AmazonS3ReadOnlyAccess \
  --region eu-west-1

aws iam detach-role-policy \
  --role-name lab11-cognito-unauthenticated-role \
  --policy-arn arn:aws:iam::aws:policy/AmazonS3ReadOnlyAccess \
  --region eu-west-1

# Delete IAM roles
aws iam delete-role \
  --role-name lab11-cognito-authenticated-role \
  --region eu-west-1

aws iam delete-role \
  --role-name lab11-cognito-unauthenticated-role \
  --region eu-west-1

# Clean up temporary files
rm -f trust-policy-auth.json trust-policy-unauth.json
```

---

## 🧪 Optional Challenges

To deepen your understanding of Amazon Cognito, try these additional challenges:

1. **Add Custom Attributes**: Modify the user pool to include custom attributes such as "firstName" and "lastName"
2. **Enable MFA**: Configure multi-factor authentication for the user pool
3. **Add a Social Identity Provider**: Configure login with Google, Facebook, or Amazon
4. **Create a Lambda Trigger**: Add a Lambda trigger for pre-sign-up validation
5. **Build a Simple Web App**: Create a basic HTML/JavaScript application that uses Cognito for authentication

---

## 🎓 Summary

In this lab, you've learned how to:
- Create and configure Amazon Cognito User Pools for user authentication
- Set up App Clients and the Hosted UI for sign-up and sign-in
- Create Identity Pools to provide authenticated users with temporary AWS credentials
- Configure IAM roles to control access to AWS services
- Test the complete authentication flow from user sign-in to accessing AWS services

These skills are essential for implementing secure authentication in modern cloud-based applications. Amazon Cognito provides a robust, scalable solution that follows security best practices without requiring you to build and maintain your own authentication system. 