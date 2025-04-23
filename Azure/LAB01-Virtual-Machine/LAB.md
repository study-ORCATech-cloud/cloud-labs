# LAB01: Launch a Virtual Machine (Azure Portal & CLI)

## 🧠 Lab Overview

Welcome to your first Azure hands-on lab! In this exercise, you'll learn how to deploy a Virtual Machine (VM) on Microsoft Azure using both the **Azure Portal (UI)** and the **Azure Command Line Interface (CLI)**. This approach gives you confidence in using both visual and command-line methods.

## 🌟 Why Azure Virtual Machines?
Azure VMs provide on-demand, scalable computing resources. They’re a fundamental part of Azure’s Infrastructure-as-a-Service (IaaS) and are commonly used for development, testing, hosting apps, or extending on-premise datacenters.

---

## 🎯 Objectives

By the end of this lab, you will be able to:

- Understand Azure VM components (resource group, region, image, size, authentication, NSG)
- Launch a virtual machine using the Azure Portal
- Launch the same VM using Azure CLI
- SSH into the VM
- Clean up resources

---

## 🛠️ Prerequisites

- Azure account ([Sign up here](https://azure.microsoft.com/en-us/free))
- [Azure CLI](https://learn.microsoft.com/en-us/cli/azure/install-azure-cli) installed and configured (`az login`)
- SSH key pair (can be generated during setup)

---

## 📝 Part 1: Launch VM Using Azure Portal (UI)

1. **Login to Azure Portal**: Go to [https://portal.azure.com](https://portal.azure.com)

2. **Create a Virtual Machine**:
   - Navigate to "Virtual Machines" > "Create"
   - Fill in the following:
     - **Subscription**: Use your free tier or main subscription
     - **Resource Group**: `lab01-ui-rg` (create if not exists)
     - **VM name**: `lab01-ui-vm`
     - **Region**: Choose a nearby region
     - **Image**: Ubuntu Server 20.04 LTS
     - **Size**: Standard B1s (free tier eligible)
     - **Authentication type**: SSH public key
     - **Username**: `azureuser`
     - **SSH public key**: Paste your key or create a new one

3. **Networking**:
   - Leave default VNet settings
   - Create a new Network Security Group (NSG)
   - Allow SSH (port 22) inbound

4. **Review and Create**:
   - Click through tabs and hit "Create"

5. **Access the VM**:
   - Once deployed, get the **Public IP** from the VM overview page
   - Connect via SSH:
     ```bash
     ssh -i path/to/your-key.pem azureuser@<PUBLIC_IP>
     ```

---

## 💻 Part 2: Launch VM Using Azure CLI

1. **Login to Azure CLI**:
   ```bash
   az login
   ```

2. **Create a Resource Group**:
   ```bash
   az group create --name lab01-cli-rg --location eastus
   ```

3. **Create a VM**:
   ```bash
   az vm create \
     --resource-group lab01-cli-rg \
     --name lab01-cli-vm \
     --image UbuntuLTS \
     --admin-username azureuser \
     --generate-ssh-keys \
     --size Standard_B1s
   ```

4. **Open Port 22 for SSH**:
   ```bash
   az vm open-port --port 22 --resource-group lab01-cli-rg --name lab01-cli-vm
   ```

5. **Get Public IP**:
   ```bash
   az vm list-ip-addresses --name lab01-cli-vm --resource-group lab01-cli-rg --output table
   ```

6. **Connect via SSH**:
   ```bash
   ssh azureuser@<PUBLIC_IP>
   ```

---

## 🧪 Optional Challenge

Install a web server on your VM:
```bash
sudo apt update
sudo apt install -y apache2
sudo systemctl start apache2
sudo systemctl enable apache2
```

Then open port 80:
```bash
az vm open-port --port 80 --resource-group lab01-cli-rg --name lab01-cli-vm
```

Open your browser to `http://<PUBLIC_IP>` to see the default Apache page.

---

## 🌀 Cleanup

Clean up your resources to avoid unnecessary charges:
```bash
az group delete --name lab01-cli-rg --yes --no-wait
```

---

## ✅ Summary

You've just created your first Azure VM using both the Azure Portal and CLI. You’ve learned about key VM settings and connected to it via SSH. This is your first step in mastering Azure infrastructure. Great work!

---

Happy Clouding ☁️

