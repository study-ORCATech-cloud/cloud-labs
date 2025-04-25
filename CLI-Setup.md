# Cloud CLI Setup Guide

## 📁 CLI Setup for Cloud Labs

This comprehensive guide helps you set up and configure the necessary Command Line Interface (CLI) tools to interact with **AWS**, **Azure**, and **Google Cloud Platform (GCP)**. Each section includes detailed installation, authentication, and configuration steps to prepare you for the hands-on cloud labs.

---

## 📦 AWS CLI Setup

### 🔧 Installation

#### macOS:
```bash
# Install using Homebrew
brew install awscli

# Verify installation
aws --version
```

#### Ubuntu/Debian:
```bash
sudo apt update
sudo apt install awscli -y

# Alternative method using pip
sudo apt install python3-pip -y
pip3 install awscli --upgrade --user

# Verify installation
aws --version
```

#### Windows:
```bash
# Using the MSI installer
# 1. Download the installer from https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html
# 2. Run the downloaded MSI file and follow the installation wizard

# Alternative using pip
pip install awscli

# Verify installation
aws --version
```

### 📖 Authentication & Configuration

#### Option 1: Using aws configure (simplest method)

1. Create an IAM user with **Programmatic access** in the AWS Console:
   - Navigate to IAM → Users → Add User
   - Enter username and select "Access key - Programmatic access"
   - Attach policies (e.g., `AdministratorAccess` for testing - use more restricted policies for production)
   - Complete user creation and save the Access Key ID and Secret Access Key

2. Configure AWS CLI:
```bash
aws configure
```

3. When prompted, provide the following information:
```
AWS Access Key ID: [your-access-key]
AWS Secret Access Key: [your-secret-key]
Default region name: eu-west-1
Default output format: json
```

#### Option 2: Using environment variables

```bash
# Set these variables in your shell session
export AWS_ACCESS_KEY_ID="your-access-key"
export AWS_SECRET_ACCESS_KEY="your-secret-key"
export AWS_DEFAULT_REGION="eu-west-1"

# Add to your shell profile (.bashrc, .zshrc, etc.) for persistence
```

#### Option 3: Using AWS profiles (for multiple accounts)

```bash
# Create a named profile
aws configure --profile project1

# Use a specific profile for commands
aws s3 ls --profile project1

# Set a profile as default for your session
export AWS_PROFILE=project1
```

### 🔢 Verify Setup
```bash
# Verify identity
aws sts get-caller-identity

# Test basic commands
aws s3 ls
aws ec2 describe-regions
```

### 🛠️ Common AWS CLI Commands

```bash
# List S3 buckets
aws s3 ls

# List EC2 instances
aws ec2 describe-instances

# Create an S3 bucket
aws s3 mb s3://my-unique-bucket-name

# Upload a file to S3
aws s3 cp local-file.txt s3://my-bucket/

# List IAM users
aws iam list-users
```

---

## 🌍 Azure CLI Setup

### 🔧 Installation

#### macOS:
```bash
# Install using Homebrew
brew install azure-cli

# Verify installation
az --version
```

#### Ubuntu/Debian:
```bash
# Import Microsoft's signing key and repository
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash

# Verify installation
az --version
```

#### Windows:
```bash
# Using the MSI installer
# 1. Download from: https://learn.microsoft.com/en-us/cli/azure/install-azure-cli-windows
# 2. Run the downloaded MSI file and follow the installation wizard

# Alternative using pip
pip install azure-cli

# Verify installation
az --version
```

### 📖 Authentication & Configuration

#### Option 1: Interactive login (simplest method)

```bash
# Start interactive login
az login

# A browser window will open for authentication
# Sign in with your Microsoft account connected to Azure
```

#### Option 2: Non-interactive login with service principal

1. Create a service principal (execute in Azure CLI after logging in):
```bash
# Create service principal and assign role
az ad sp create-for-rbac --name "MyAppSP" --role "Contributor" --scopes /subscriptions/<subscription-id>

# This command outputs JSON with appId, password, tenant values you'll need
```

2. Login with service principal:
```bash
az login --service-principal --username <appId> --password <password> --tenant <tenant>
```

#### Option 3: Using environment variables

```bash
# Set these variables in your shell session
export AZURE_SUBSCRIPTION_ID="your-subscription-id"
export AZURE_TENANT_ID="your-tenant-id"
export AZURE_CLIENT_ID="your-client-id"
export AZURE_CLIENT_SECRET="your-client-secret"
```

### 🔢 Set Default Subscription
```bash
# List all subscriptions
az account list --output table

# Set default subscription
az account set --subscription "<SUBSCRIPTION-ID-OR-NAME>"
```

### 🔢 Verify Setup
```bash
# Verify account 
az account show

# Test basic commands
az group list --output table
```

### 🛠️ Common Azure CLI Commands

```bash
# List resource groups
az group list --output table

# List virtual machines
az vm list --output table

# Create a resource group
az group create --name myResourceGroup --location westeurope

# Create a storage account
az storage account create --name mystorageacct --resource-group myResourceGroup --sku Standard_LRS

# List Azure regions
az account list-locations --output table
```

---

## 🌐 GCP CLI (gcloud) Setup

### 🔧 Installation

#### macOS:
```bash
# Using Homebrew
brew install --cask google-cloud-sdk

# Verify installation
gcloud --version
```

#### Ubuntu/Debian:
```bash
# Add the Cloud SDK distribution URI as a package source
echo "deb [signed-by=/usr/share/keyrings/cloud.google.gpg] https://packages.cloud.google.com/apt cloud-sdk main" | sudo tee -a /etc/apt/sources.list.d/google-cloud-sdk.list

# Import the Google Cloud public key
curl https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key --keyring /usr/share/keyrings/cloud.google.gpg add -

# Update and install the Cloud SDK
sudo apt-get update && sudo apt-get install google-cloud-sdk

# Verify installation
gcloud --version
```

#### Windows:
```bash
# 1. Download installer: https://cloud.google.com/sdk/docs/install
# 2. Run the installer and follow the installation wizard
# 3. Verify installation by opening Google Cloud SDK Shell from Start menu
gcloud --version
```

### 📖 Authentication & Configuration

#### Option 1: Interactive initialization (simplest method)

```bash
# Initialize gcloud
gcloud init
```

This interactive process will:
- Open browser for authentication
- Let you select a cloud project
- Set default region and zone

#### Option 2: Non-interactive authentication with service account

1. Create a service account in GCP Console:
   - Navigate to IAM & Admin → Service Accounts
   - Create a service account with appropriate permissions
   - Create and download a key file (JSON)

2. Authenticate with the service account:
```bash
# Set the environment variable to point to your key file
export GOOGLE_APPLICATION_CREDENTIALS="/path/to/your-service-account-key.json"

# Activate the service account
gcloud auth activate-service-account --key-file=$GOOGLE_APPLICATION_CREDENTIALS
```

#### Option 3: Application Default Credentials (ADC)

```bash
# Login with application default credentials
gcloud auth application-default login
```

### 🔢 Set Project, Region, and Zone
```bash
# Set default project
gcloud config set project "your-project-id"

# Set default region
gcloud config set compute/region us-central1

# Set default zone
gcloud config set compute/zone us-central1-a
```

### 🔢 Verify Setup
```bash
# View current configuration
gcloud config list

# Test connectivity
gcloud compute zones list
```

### 🛠️ Common GCP CLI Commands

```bash
# List all projects
gcloud projects list

# List compute instances
gcloud compute instances list

# Create a VM instance
gcloud compute instances create my-vm --machine-type=e2-medium --zone=us-central1-a

# List Cloud Storage buckets
gsutil ls

# Create a new Cloud Storage bucket
gsutil mb gs://my-unique-bucket-name
```

---

## 🚧 Advanced Troubleshooting Tips

### AWS CLI Troubleshooting
- **Invalid credentials**: Run `aws configure` again or check `~/.aws/credentials`
- **Access denied errors**: Verify IAM permissions and policy attachments
- **Region issues**: Ensure resources exist in your configured region with `aws configure get region`
- **Throttling errors**: Implement exponential backoff and request rate limiting
- **Debug mode**: Add `--debug` to any command for detailed API call information

### Azure CLI Troubleshooting
- **Authentication failures**: Run `az login` again or check token expiration with `az account get-access-token`
- **Permission issues**: Verify role assignments with `az role assignment list --assignee [your-email]`
- **Subscription context**: Ensure you're operating in the correct subscription with `az account show`
- **Extension problems**: Update extensions with `az extension update --name [extension-name]`
- **Debug mode**: Add `--debug` to any command for more detailed logs

### GCP CLI Troubleshooting
- **Authentication issues**: Verify active account with `gcloud auth list`
- **Project access**: Check if you have permission to access the project
- **API enablement**: Ensure required APIs are enabled with `gcloud services list`
- **Quota limits**: Check your quotas in the Google Cloud Console
- **Proxy issues**: Configure proxy settings if needed with `gcloud config set proxy/type http`
- **Verbose logging**: Add `--verbosity=debug` to any command for detailed logs

---

## 🔒 Security Best Practices

- **Least privilege**: Use minimal permissions needed for your tasks
- **Rotate credentials**: Regularly rotate access keys and service account keys
- **Use roles**: Prefer role-based access control over direct permissions
- **Secure credentials**: Never commit credentials to source control
- **MFA**: Enable multi-factor authentication for cloud accounts
- **Use named profiles**: Create separate profiles for different projects or environments
- **Audit CLI usage**: Enable CloudTrail (AWS), Activity Logs (Azure), or Cloud Audit Logs (GCP)

---

## 🎯 What's Next?

Once your CLI tools are set up and verified, you're ready to start with the labs!

1. **Practice basic commands** to become familiar with the syntax:
```bash
# Examples for each cloud provider
aws ec2 describe-instances
az vm list
gcloud compute instances list
```

2. **Explore help documentation** directly from the CLI:
```bash
aws help
az --help
gcloud --help
```

3. **Start with LAB01** of your preferred cloud provider to apply your CLI skills.

4. **Learn shell scripting** to automate repetitive CLI tasks.

You're now ready to master cloud infrastructure—one command at a time! 🚀

