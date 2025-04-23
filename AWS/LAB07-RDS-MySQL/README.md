# AWS LAB07: Resource Overview – Amazon RDS (MySQL)

## 🗄️ What Resources Are We Working With?

In **AWS LAB07**, you’ll provision a managed **Relational Database Service (RDS)** instance running **MySQL**. This lab demonstrates how AWS handles database provisioning, patching, backup, and scaling — all without requiring direct OS-level management.

---

## 🧮 Amazon RDS

### Description
RDS makes it easy to set up, operate, and scale relational databases in the cloud.

### Key Concepts
- **Database Engine**: MySQL (other options include PostgreSQL, MariaDB, etc.)
- **DB Instance**: A dedicated compute instance running your database
- **Multi-AZ Deployment**: Ensures high availability
- **Storage Type**: General Purpose (SSD) or Provisioned IOPS

### What You Do in the Lab
- Create a MySQL RDS instance
- Set master username and password
- Configure public access for testing (disable in production!)

---

## 🔐 Security Group

### Description
A security group acts as a firewall for your RDS instance.

### What You Do in the Lab
- Create or modify a security group to allow inbound MySQL (port 3306)
- Restrict access to your own IP address

---

## 🗺️ Parameter and Option Groups

### Description
Used for fine-tuning and extending RDS instance configurations.

### What You Do in the Lab
- Leave defaults, or optionally explore minor parameter changes

---

## 📊 Monitoring with CloudWatch

### Description
RDS metrics like CPU, memory, and storage are sent to CloudWatch.

### What You Do in the Lab
- View metrics like `DatabaseConnections` and `FreeStorageSpace`
- Optionally create an alarm for low storage

---

## ✅ Summary

| Resource            | Purpose                                           | Created Using    |
|---------------------|--------------------------------------------------|------------------|
| Amazon RDS Instance | Managed relational database                      | Console & CLI    |
| Security Group      | Controls inbound MySQL access                    | Console & CLI    |
| CloudWatch Metrics  | Monitors DB health and performance               | Console          |
| Parameter Group     | Configures engine-specific DB settings           | Console (optional)|

RDS abstracts database infrastructure management, making it ideal for developers and DevOps teams looking to focus on application logic rather than low-level maintenance.