# Common/CLI-Setup.md

## 📁 CLI Setup for Cloud Labs

This guide helps you set up the necessary CLI tools to interact with **AWS**, **Azure**, and **Google Cloud Platform (GCP)**. Each section includes installation, authentication, and basic configuration steps, so you're ready to use the CLI tools for hands-on labs.

---

## 📦 AWS CLI Setup

### 🔧 Installation

#### macOS:
```bash
brew install awscli
```

#### Ubuntu/Debian:
```bash
sudo apt update
sudo apt install awscli -y
```

#### Windows:
- Download the installer: [AWS CLI Installer](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)

### 📖 Configuration

1. Create an IAM user with **Programmatic access**.
2. Attach the necessary policies (e.g., `AdministratorAccess` for testing).
3. Run:
```bash
aws configure
```
4. Enter your Access Key, Secret Key, region (e.g., `us-east-1`), and default output format (e.g., `json`).

### 🔢 Verify Setup
```bash
aws sts get-caller-identity
```

---

## 🌍 Azure CLI Setup

### 🔧 Installation

#### macOS:
```bash
brew install azure-cli
```

#### Ubuntu/Debian:
```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

#### Windows:
- Download from: [Azure CLI Installer](https://learn.microsoft.com/en-us/cli/azure/install-azure-cli)

### 📖 Configuration

1. Login with:
```bash
az login
```
2. A browser window will open for authentication.

### 🔢 Set Default Subscription
```bash
az account set --subscription "<YOUR-SUBSCRIPTION-NAME>"
```

### 🔢 Verify Setup
```bash
az account show
```

---

## 🌐 GCP CLI (gcloud) Setup

### 🔧 Installation

#### macOS/Linux:
```bash
curl -O https://dl.google.com/dl/cloudsdk/channels/rapid/downloads/google-cloud-cli-<version>-<platform>.tar.gz
```
Or use:
```bash
brew install --cask google-cloud-sdk
```

#### Windows:
- Download installer: [Google Cloud SDK](https://cloud.google.com/sdk/docs/install)

### 📖 Initialization
```bash
gcloud init
```
Follow the prompts to:
- Authenticate with Google
- Set the active project
- Set the default compute region/zone

### 🔢 Verify Setup
```bash
gcloud config list
```

---

## 🚧 Troubleshooting Tips

- Ensure your CLI tools are up to date.
- Use `--help` with any CLI command to explore options.
- Check IAM permissions for access errors.
- Ensure environment variables aren't conflicting with CLI configurations.

---

## 🎯 What's Next?

Once your CLI tools are set up and verified:
- Try listing resources like VMs or storage buckets
- Use CLI to deploy simple infrastructure manually
- Start working on LAB01 of each cloud provider

```bash
# Examples:
aws ec2 describe-instances
az vm list
gcloud compute instances list
```

You're now ready to automate the cloud—one command at a time! 🚀

