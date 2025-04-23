# LAB01: Launch a Virtual Machine (GCP Console & gcloud CLI)

## 🧠 Lab Overview

In this lab, you will deploy a Virtual Machine (VM) instance on **Google Cloud Platform (GCP)** using both the **Google Cloud Console (UI)** and the **gcloud Command Line Interface (CLI)**. You'll gain experience with GCP's Compute Engine and understand the key components of VM provisioning.

## 🌟 Why GCP Compute Engine?
Compute Engine lets you create and run virtual machines on Google’s infrastructure. It offers reliable performance, flexible configurations, and integration with Google Cloud services.

---

## 🎯 Objectives

By the end of this lab, you will:

- Understand GCP VM components: project, zone, machine type, image, firewall rules
- Deploy a VM instance using the GCP Console
- Deploy the same VM using gcloud CLI
- SSH into your VM
- Configure basic firewall rules
- Clean up resources

---

## 🛠️ Prerequisites

- Google Cloud account ([Free trial available](https://cloud.google.com/free))
- [gcloud CLI](https://cloud.google.com/sdk/docs/install) installed
- Billing enabled on your GCP project
- Default project set in CLI (`gcloud config set project <your-project-id>`)

---

## 📝 Part 1: Launch VM Using GCP Console (UI)

1. **Login to Google Cloud Console**: [https://console.cloud.google.com](https://console.cloud.google.com)

2. **Enable Compute Engine API** (if prompted)

3. **Navigate to VM Instances**:
   - Go to "Compute Engine" > "VM instances"
   - Click **Create Instance**

4. **Configure the VM**:
   - **Name**: `lab01-ui-vm`
   - **Region/Zone**: Choose closest to you (e.g., `us-central1-a`)
   - **Machine Type**: `e2-micro` (free tier eligible)
   - **Boot Disk**: Ubuntu 20.04 LTS
   - **Firewall**: Check **Allow HTTP** and **Allow HTTPS** (optional)

5. **Click Create**

6. **Access the VM**:
   - Click SSH from the VM list or use external tools with provided IP address

---

## 💻 Part 2: Launch VM Using gcloud CLI

1. **Login via CLI**:
   ```bash
   gcloud auth login
   ```

2. **Set default project and zone**:
   ```bash
   gcloud config set project <your-project-id>
   gcloud config set compute/zone us-central1-a
   ```

3. **Create a VM instance**:
   ```bash
   gcloud compute instances create lab01-cli-vm \
       --machine-type=e2-micro \
       --image-family=ubuntu-2004-lts \
       --image-project=ubuntu-os-cloud \
       --tags=http-server,https-server \
       --boot-disk-size=10GB
   ```

4. **Open Firewall for HTTP**:
   ```bash
   gcloud compute firewall-rules create allow-http \
       --allow tcp:80 \
       --target-tags=http-server \
       --description="Allow HTTP traffic" \
       --direction=INGRESS
   ```

5. **SSH into your VM**:
   ```bash
   gcloud compute ssh lab01-cli-vm
   ```

---

## 🧪 Optional Challenge: Deploy a Web Server

Install Apache and serve a basic page:
```bash
sudo apt update
sudo apt install -y apache2
sudo systemctl start apache2
sudo systemctl enable apache2
```

Open your browser to `http://<EXTERNAL_IP>` and view the Apache default page.

---

## 🌀 Cleanup

To avoid charges, delete the VM:
```bash
# Using CLI
gcloud compute instances delete lab01-cli-vm --quiet

# Or delete from GCP Console
```

Delete firewall rule (if created):
```bash
gcloud compute firewall-rules delete allow-http --quiet
```

---

## ✅ Summary

You’ve successfully deployed and connected to a GCP virtual machine using both the Console and CLI. You’ve explored VM configuration and basic network access. You’re now one step closer to mastering GCP!

---

Happy Clouding ☁️

