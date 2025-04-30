# LAB19: Secure Configuration Management with AWS Secrets Manager and Parameter Store

## ⚡ Lab Overview

In this hands-on lab, you'll work with **AWS Secrets Manager** and **AWS Systems Manager Parameter Store**, two powerful services that provide secure storage and management of sensitive information and application configurations. You'll learn how to securely store, retrieve, rotate, and manage secrets like database credentials and API keys, as well as application configuration data in a hierarchical, organized manner.

### 💼 Real-World DevOps Applications

Secrets Manager and Parameter Store are critical components in modern application infrastructure, offering several key advantages:

- **Security**: Eliminate hardcoded credentials from application code and infrastructure
- **Centralization**: Manage all secrets and configurations from a single location
- **Automation**: Integrate with CI/CD pipelines for secure deployments
- **Rotation**: Automatically update credentials on a regular schedule
- **Auditing**: Track access and changes to sensitive information
- **Scalability**: Manage configurations across multiple environments and applications

Common production use cases include:

- **Database Credentials**: Securely store and rotate database usernames and passwords
- **API Keys**: Manage third-party API credentials without exposing them in code
- **OAuth Tokens**: Store authentication tokens for service-to-service communication
- **Application Configurations**: Manage environment-specific settings
- **Feature Flags**: Control feature availability across environments
- **Certificate Management**: Store SSL/TLS certificates and private keys
- **Encryption Keys**: Manage encryption keys for application data

---

## 🎯 Objectives

By the end of this lab, you will be able to:
- Create and manage secrets in AWS Secrets Manager
- Set up automatic rotation for database credentials
- Implement resource-based policies for secret access control
- Create and organize parameters in Parameter Store with hierarchies
- Use parameter versions and parameter policies
- Store encrypted secrets using SecureString parameters
- Access secrets and parameters using AWS CLI and AWS SDK
- Implement a sample application that uses both services
- Apply best practices for secrets and configuration management

---

## 🛠️ Prerequisites

- AWS account with administrative permissions
- Basic understanding of security concepts
- Familiarity with AWS services (particularly IAM)
- AWS CLI installed and configured (refer to [CLI Setup Guide](../../CLI-Setup.md))
- Basic Python knowledge for sample application

---

# 🌐 PART 1: AWS MANAGEMENT CONSOLE IMPLEMENTATION

In this first part of the lab, you'll create and configure Secrets Manager and Parameter Store resources using the AWS Management Console.

## 📊 Step 1: Create a Secret in AWS Secrets Manager

1. Sign in to the AWS Management Console
2. Navigate to the **AWS Secrets Manager** service
3. Click **Store a new secret**
4. For **Secret type**, select **Other type of secret**
5. For **Key/value pairs**, create the following pairs:
   - Key: `username`, Value: `admin`
   - Key: `password`, Value: `Lab19-Password!`
   - Click **Add row** to add another pair
   - Key: `api_key`, Value: `lab19-api-key-12345`
6. For **Encryption key**, leave the default **aws/secretsmanager** KMS key
7. Click **Next**
8. For **Secret name**, enter `lab19/application/credentials`
9. For **Description**, enter `Application credentials for Lab 19`
10. Add tags (optional):
    - Key: `Environment`, Value: `Development`
    - Key: `Project`, Value: `Lab19`
11. Click **Next**
12. For **Automatic rotation**, select **Disable automatic rotation** (we'll set this up later)
13. Click **Next**
14. Review the configuration and click **Store**

## 🔐 Step 2: Create a Secret with Resource-Based Policy

Now let's create another secret with a resource-based policy to control access:

1. Click **Store a new secret**
2. For **Secret type**, select **Other type of secret**
3. For **Key/value pairs**, create:
   - Key: `database_password`, Value: `SecureDBPassword!`
4. Click **Next**
5. For **Secret name**, enter `lab19/database/credentials`
6. For **Description**, enter `Database credentials for Lab 19`
7. Click **Next**
8. For **Automatic rotation**, select **Disable automatic rotation**
9. Click **Next**
10. Review and click **Store**
11. Once created, select the secret from the list
12. Go to the **Resource permissions** tab
13. Click **Edit permissions**
14. Paste the following resource-based policy (this is a sample policy that allows read-only access to the secret):
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "AWS": "*"
      },
      "Action": "secretsmanager:GetSecretValue",
      "Resource": "*",
      "Condition": {
        "StringEquals": {
          "aws:PrincipalTag/Role": "DBReader"
        }
      }
    }
  ]
}
```
15. Click **Save**

## 🔄 Step 3: Configure Automatic Rotation for a Secret

Now, let's set up automatic rotation for one of our secrets:

1. Navigate back to the Secrets Manager dashboard
2. Select the secret `lab19/application/credentials`
3. Go to the **Rotation configuration** tab
4. Click **Edit rotation**
5. For **Rotation schedule**, select **Schedule automatic rotation**
6. For **Rotation schedule**, set:
   - **Days**: `30` (rotate every 30 days)
   - **Time (UTC)**: Choose a convenient time
7. For **Rotation function**, select **Create a new Lambda function**
8. For **Function name**, enter `lab19-secret-rotation-function`
9. For **Using rotation strategy**, select **Single user**
10. Click **Save**

Note: AWS will create a Lambda function to handle the rotation. In a real production environment, you would customize this function based on your specific needs.

## 📝 Step 4: Create Parameters in Parameter Store

Now, let's work with Parameter Store to create various parameter types:

1. Navigate to the **AWS Systems Manager** service
2. In the left navigation pane, under **Application Management**, click **Parameter Store**
3. Click **Create parameter**
4. For **Name**, enter `/lab19/app/environment`
5. For **Description**, enter `Application environment setting`
6. For **Tier**, select **Standard**
7. For **Type**, select **String**
8. For **Data type**, select **text**
9. For **Value**, enter `development`
10. Click **Create parameter**

## 📁 Step 5: Create a SecureString Parameter

1. Click **Create parameter**
2. For **Name**, enter `/lab19/app/secure/apikey`
3. For **Description**, enter `Secure API key for third-party service`
4. For **Tier**, select **Standard**
5. For **Type**, select **SecureString**
6. For **KMS key source**, leave the default **My current account**
7. For **KMS Key ID**, leave the default **alias/aws/ssm**
8. For **Value**, enter `secure-api-key-9876543210`
9. Click **Create parameter**

## 📋 Step 6: Create a StringList Parameter

1. Click **Create parameter**
2. For **Name**, enter `/lab19/app/allowed-origins`
3. For **Description**, enter `List of allowed origins for CORS`
4. For **Tier**, select **Standard**
5. For **Type**, select **StringList**
6. For **Value**, enter `https://example.com,https://dev.example.com,https://test.example.com`
7. Click **Create parameter**

## 🌲 Step 7: Create a Parameter Hierarchy

Create a hierarchy of parameters to organize configuration for different environments:

1. Create the following parameters:
   - Name: `/lab19/production/database/host`
     - Type: String
     - Value: `prod-db.example.com`
   
   - Name: `/lab19/production/database/port`
     - Type: String
     - Value: `3306`
   
   - Name: `/lab19/production/feature-flags/new-ui`
     - Type: String
     - Value: `false`
   
   - Name: `/lab19/staging/database/host`
     - Type: String
     - Value: `staging-db.example.com`
   
   - Name: `/lab19/staging/database/port`
     - Type: String
     - Value: `3306`
   
   - Name: `/lab19/staging/feature-flags/new-ui`
     - Type: String
     - Value: `true`

## ⏱️ Step 8: Create a Parameter with Parameter Policy

1. Click **Create parameter**
2. For **Name**, enter `/lab19/app/temp-access-token`
3. For **Description**, enter `Temporary access token with expiration`
4. For **Tier**, select **Advanced**
5. For **Type**, select **SecureString**
6. For **Value**, enter `temp-token-xyz-12345`
7. Expand **Parameter policies**
8. In the policy field, enter the following JSON (this policy will expire the parameter after 15 days and notify 3 days before expiration):
```json
{
  "Type": "Expiration",
  "Version": "1.0",
  "Attributes": {
    "Timestamp": "{{ts+15d}}",
    "NotificationState": "BeforeExpiration",
    "NotifyBeforeExpireTime": "3d"
  }
}
```
9. Click **Create parameter**

## 🔍 Step 9: View Parameter History

1. Select a parameter you've created (preferably one you've modified)
2. Click on the **History** tab to view the history of changes
3. Note the version numbers, which increment with each change

## 🧪 Step 10: Create a Simple Python Script to Access Secrets and Parameters

Let's create a simple Python script that shows how to access secrets and parameters programmatically.

1. Create a file called `access_secrets.py` on your local machine with the following content:

```python
import boto3
import json
from botocore.exceptions import ClientError

def get_secret(secret_name):
    """
    Retrieve a secret from AWS Secrets Manager
    """
    region_name = "eu-west-1"
    
    # Create a Secrets Manager client
    session = boto3.session.Session()
    client = session.client(
        service_name='secretsmanager',
        region_name=region_name
    )
    
    try:
        get_secret_value_response = client.get_secret_value(
            SecretId=secret_name
        )
    except ClientError as e:
        print(f"Error retrieving secret {secret_name}: {e}")
        return None
    
    # Parse and return the secret
    if 'SecretString' in get_secret_value_response:
        secret = get_secret_value_response['SecretString']
        return json.loads(secret)
    else:
        return None

def get_parameter(parameter_name, with_decryption=False):
    """
    Retrieve a parameter from AWS Systems Manager Parameter Store
    """
    region_name = "eu-west-1"
    
    # Create a Parameter Store client
    session = boto3.session.Session()
    client = session.client(
        service_name='ssm',
        region_name=region_name
    )
    
    try:
        response = client.get_parameter(
            Name=parameter_name,
            WithDecryption=with_decryption
        )
        return response['Parameter']['Value']
    except ClientError as e:
        print(f"Error retrieving parameter {parameter_name}: {e}")
        return None

def get_parameters_by_path(parameter_path, with_decryption=False):
    """
    Retrieve multiple parameters by path from AWS Systems Manager Parameter Store
    """
    region_name = "eu-west-1"
    
    # Create a Parameter Store client
    session = boto3.session.Session()
    client = session.client(
        service_name='ssm',
        region_name=region_name
    )
    
    try:
        response = client.get_parameters_by_path(
            Path=parameter_path,
            Recursive=True,
            WithDecryption=with_decryption
        )
        
        parameters = {}
        for param in response['Parameters']:
            name = param['Name']
            value = param['Value']
            parameters[name] = value
            
        return parameters
    except ClientError as e:
        print(f"Error retrieving parameters by path {parameter_path}: {e}")
        return None

def main():
    # Get secrets from Secrets Manager
    print("Retrieving secrets from AWS Secrets Manager:")
    app_credentials = get_secret('lab19/application/credentials')
    if app_credentials:
        print(f"Application Username: {app_credentials.get('username')}")
        print(f"Application API Key: {app_credentials.get('api_key')}")
        # Note: In a real application, you would never print passwords to logs
        print("Application Password: ********")
    
    # Get parameters from Parameter Store
    print("\nRetrieving parameters from AWS Systems Manager Parameter Store:")
    environment = get_parameter('/lab19/app/environment')
    print(f"Application Environment: {environment}")
    
    # Get secure parameter
    api_key = get_parameter('/lab19/app/secure/apikey', True)
    print(f"Secure API Key: {api_key}")
    
    # Get StringList parameter
    allowed_origins = get_parameter('/lab19/app/allowed-origins')
    print(f"Allowed Origins: {allowed_origins}")
    
    # Get parameters by path
    print("\nRetrieving staging environment parameters:")
    staging_params = get_parameters_by_path('/lab19/staging/')
    for name, value in staging_params.items():
        print(f"{name}: {value}")

if __name__ == "__main__":
    main()
```

This script demonstrates how to:
- Retrieve secrets from AWS Secrets Manager
- Get individual parameters from Parameter Store
- Get parameters by path (hierarchically)
- Handle secure parameters with decryption

Note: You would run this script on a machine with AWS credentials configured and with the necessary permissions to access these resources.

---

# 💻 PART 2: AWS CLI IMPLEMENTATION

In this section, you'll perform the same operations using the AWS CLI.

## 📊 Step 1: Create a Secret in AWS Secrets Manager

```bash
# Create a secret with multiple key-value pairs
aws secretsmanager create-secret \
    --name "lab19/cli/application/credentials" \
    --description "Application credentials created via CLI" \
    --secret-string '{"username":"admin-cli","password":"CliPassword123!","api_key":"cli-api-key-98765"}' \
    --region eu-west-1
```

## 🔐 Step 2: Create a Secret with Resource-Based Policy

```bash
# First create the secret
aws secretsmanager create-secret \
    --name "lab19/cli/database/credentials" \
    --description "Database credentials created via CLI" \
    --secret-string '{"database_password":"CliDBPassword456!"}' \
    --region eu-west-1

# Then attach a resource-based policy
aws secretsmanager put-resource-policy \
    --secret-id "lab19/cli/database/credentials" \
    --resource-policy '{
      "Version": "2012-10-17",
      "Statement": [
        {
          "Effect": "Allow",
          "Principal": {
            "AWS": "*"
          },
          "Action": "secretsmanager:GetSecretValue",
          "Resource": "*",
          "Condition": {
            "StringEquals": {
              "aws:PrincipalTag/Role": "DBReaderCLI"
            }
          }
        }
      ]
    }' \
    --region eu-west-1
```

## 🔍 Step 3: Retrieve Secret Values

```bash
# Retrieve secret value
aws secretsmanager get-secret-value \
    --secret-id "lab19/cli/application/credentials" \
    --region eu-west-1
```

## 📝 Step 4: Create Parameters in Parameter Store

```bash
# Create a String parameter
aws ssm put-parameter \
    --name "/lab19/cli/app/environment" \
    --description "Application environment setting via CLI" \
    --type "String" \
    --value "development-cli" \
    --region eu-west-1

# Create a SecureString parameter
aws ssm put-parameter \
    --name "/lab19/cli/app/secure/apikey" \
    --description "Secure API key created via CLI" \
    --type "SecureString" \
    --value "cli-secure-api-key-0987654321" \
    --region eu-west-1

# Create a StringList parameter
aws ssm put-parameter \
    --name "/lab19/cli/app/allowed-origins" \
    --description "List of allowed origins for CORS via CLI" \
    --type "StringList" \
    --value "https://cli.example.com,https://cli-dev.example.com" \
    --region eu-west-1
```

## 🌲 Step 5: Create a Parameter Hierarchy

```bash
# Create a hierarchy of parameters
aws ssm put-parameter \
    --name "/lab19/cli/production/database/host" \
    --type "String" \
    --value "cli-prod-db.example.com" \
    --region eu-west-1

aws ssm put-parameter \
    --name "/lab19/cli/production/database/port" \
    --type "String" \
    --value "3306" \
    --region eu-west-1

aws ssm put-parameter \
    --name "/lab19/cli/staging/database/host" \
    --type "String" \
    --value "cli-staging-db.example.com" \
    --region eu-west-1

aws ssm put-parameter \
    --name "/lab19/cli/staging/database/port" \
    --type "String" \
    --value "3306" \
    --region eu-west-1
```

## ⏱️ Step 6: Create a Parameter with Parameter Policy

```bash
# Create an advanced parameter with expiration policy
aws ssm put-parameter \
    --name "/lab19/cli/app/temp-access-token" \
    --description "Temporary access token with expiration via CLI" \
    --type "SecureString" \
    --value "cli-temp-token-abc-67890" \
    --tier "Advanced" \
    --policies '[{"Type":"Expiration","Version":"1.0","Attributes":{"Timestamp":"{{ts+15d}}","NotificationState":"BeforeExpiration","NotifyBeforeExpireTime":"3d"}}]' \
    --region eu-west-1
```

## 🔍 Step 7: Retrieve Parameters

```bash
# Get a single parameter
aws ssm get-parameter \
    --name "/lab19/cli/app/environment" \
    --region eu-west-1

# Get a secure parameter with decryption
aws ssm get-parameter \
    --name "/lab19/cli/app/secure/apikey" \
    --with-decryption \
    --region eu-west-1

# Get parameters by path
aws ssm get-parameters-by-path \
    --path "/lab19/cli/production/" \
    --recursive \
    --region eu-west-1
```

## 📜 Step 8: Get Parameter History

```bash
# Get parameter history
aws ssm get-parameter-history \
    --name "/lab19/cli/app/environment" \
    --region eu-west-1
```

## 🔄 Step A: Update a Parameter Value

```bash
# Update parameter value
aws ssm put-parameter \
    --name "/lab19/cli/app/environment" \
    --value "testing-cli" \
    --type "String" \
    --overwrite \
    --region eu-west-1
```

## 🗑️ Step 9: Clean Up Resources

When you're finished with the lab, clean up the resources you created:

**Secrets Manager:**
```bash
# Delete secrets
aws secretsmanager delete-secret \
    --secret-id "lab19/application/credentials" \
    --force-delete-without-recovery \
    --region eu-west-1

aws secretsmanager delete-secret \
    --secret-id "lab19/database/credentials" \
    --force-delete-without-recovery \
    --region eu-west-1

aws secretsmanager delete-secret \
    --secret-id "lab19/cli/application/credentials" \
    --force-delete-without-recovery \
    --region eu-west-1

aws secretsmanager delete-secret \
    --secret-id "lab19/cli/database/credentials" \
    --force-delete-without-recovery \
    --region eu-west-1
```

**Parameter Store:**
```bash
# Delete parameters (example for a few parameters)
aws ssm delete-parameter \
    --name "/lab19/app/environment" \
    --region eu-west-1

aws ssm delete-parameter \
    --name "/lab19/app/secure/apikey" \
    --region eu-west-1

# To delete multiple parameters by path, first list them
parameters=$(aws ssm get-parameters-by-path \
    --path "/lab19/" \
    --recursive \
    --query "Parameters[].Name" \
    --output text \
    --region eu-west-1)

# Then delete each parameter
for param in $parameters; do
    aws ssm delete-parameter \
        --name "$param" \
        --region eu-west-1
    echo "Deleted parameter: $param"
done
```

**Lambda Rotation Function (if created):**
```bash
# Delete the Lambda rotation function
aws lambda delete-function \
    --function-name "lab19-secret-rotation-function" \
    --region eu-west-1
```

---

# 🚀 Lab Extension: Building a Sample Application

## 🔧 Create a Simple Flask Web Application

For a more practical example, let's create a simple Flask web application that uses Secrets Manager and Parameter Store:

1. Create a file called `app.py` with the following content:

```python
from flask import Flask, jsonify
import boto3
import json
from botocore.exceptions import ClientError

app = Flask(__name__)

# AWS region
region = "eu-west-1"

# Create clients
secretsmanager_client = boto3.client('secretsmanager', region_name=region)
ssm_client = boto3.client('ssm', region_name=region)

def get_secret(secret_name):
    """Retrieve a secret from AWS Secrets Manager"""
    try:
        response = secretsmanager_client.get_secret_value(SecretId=secret_name)
        if 'SecretString' in response:
            return json.loads(response['SecretString'])
    except ClientError as e:
        app.logger.error(f"Error retrieving secret {secret_name}: {e}")
    return None

def get_parameter(parameter_name, with_decryption=False):
    """Retrieve a parameter from AWS Systems Manager Parameter Store"""
    try:
        response = ssm_client.get_parameter(
            Name=parameter_name, 
            WithDecryption=with_decryption
        )
        return response['Parameter']['Value']
    except ClientError as e:
        app.logger.error(f"Error retrieving parameter {parameter_name}: {e}")
    return None

def get_parameters_by_path(path, with_decryption=False):
    """Retrieve parameters by path from Parameter Store"""
    try:
        response = ssm_client.get_parameters_by_path(
            Path=path,
            Recursive=True,
            WithDecryption=with_decryption
        )
        
        parameters = {}
        for param in response['Parameters']:
            name = param['Name']
            value = param['Value']
            # Extract just the parameter name without the full path
            short_name = name.split('/')[-1]
            parameters[short_name] = value
        
        return parameters
    except ClientError as e:
        app.logger.error(f"Error retrieving parameters by path {path}: {e}")
    return {}

@app.route('/')
def index():
    return jsonify({
        "message": "Welcome to the Secrets and Parameters Demo App",
        "endpoints": [
            "/environment",
            "/config",
            "/api-credentials"
        ]
    })

@app.route('/environment')
def environment():
    env = get_parameter('/lab19/app/environment')
    return jsonify({
        "environment": env or "unknown"
    })

@app.route('/config')
def config():
    # Get environment
    env = get_parameter('/lab19/app/environment')
    
    # Get configuration based on environment
    if env:
        config_path = f"/lab19/{env}"
        configs = get_parameters_by_path(config_path)
        return jsonify({
            "environment": env,
            "configuration": configs
        })
    
    return jsonify({
        "error": "Could not determine environment"
    }), 500

@app.route('/api-credentials')
def api_credentials():
    # Get API credentials (username and API key only, not password)
    credentials = get_secret('lab19/application/credentials')
    
    if credentials:
        return jsonify({
            "username": credentials.get('username', 'unknown'),
            "api_key_length": len(credentials.get('api_key', '')) if 'api_key' in credentials else 0
        })
    
    return jsonify({
        "error": "Could not retrieve API credentials"
    }), 500

if __name__ == '__main__':
    app.run(debug=True, host='0.0.0.0', port=5000)
```

2. Create a `requirements.txt` file with the following dependencies:
```
flask==2.0.1
boto3==1.18.0
```

3. Install the dependencies:
```bash
pip install -r requirements.txt
```

4. Run the application:
```bash
python app.py
```

5. Access the application endpoints to verify it retrieves secrets and parameters correctly:
   - http://localhost:5000/
   - http://localhost:5000/environment
   - http://localhost:5000/config
   - http://localhost:5000/api-credentials

This sample application demonstrates:
- How to retrieve secrets and parameters in a web application
- Best practices for not exposing sensitive values (note how password is not returned)
- How to use environment-specific configurations
- Proper error handling for secret and parameter retrieval

---

# 🧠 Key Learnings

In this lab, you've learned:

1. The differences between AWS Secrets Manager and Parameter Store and when to use each
2. How to securely store and retrieve sensitive information
3. Best practices for organizing parameters in hierarchies
4. Techniques for automating secret rotation
5. How to use resource policies to control access to secrets
6. Methods for managing parameter expiration and versioning
7. Practical implementation of secure configuration in a sample application

By applying these practices in your own projects, you can significantly improve the security posture of your applications by removing hardcoded credentials and sensitive information from your codebase.

---

# 📚 Additional Resources

- [AWS Secrets Manager Documentation](https://docs.aws.amazon.com/secretsmanager/latest/userguide/intro.html)
- [AWS Systems Manager Parameter Store Documentation](https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-parameter-store.html)
- [AWS Secrets Manager Rotation](https://docs.aws.amazon.com/secretsmanager/latest/userguide/rotating-secrets.html)
- [Parameter Store Parameter Tiers](https://docs.aws.amazon.com/systems-manager/latest/userguide/parameter-store-advanced-parameters.html)
- [AWS Security Best Practices](https://docs.aws.amazon.com/wellarchitected/latest/security-pillar/welcome.html) 