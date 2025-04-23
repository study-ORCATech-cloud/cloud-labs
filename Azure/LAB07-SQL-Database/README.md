# Azure LAB07: Resource Overview – Azure SQL Database

## 🗃️ What Resources Are We Working With?

In **Azure LAB07**, you’ll deploy a fully managed **Azure SQL Database**, configure access with **firewall rules**, and connect using SQL Server authentication. This lab demonstrates secure and scalable database hosting without managing the OS or patches.

---

## 🧮 Azure SQL Database

### Description
A PaaS (Platform-as-a-Service) offering for relational database management that removes the need to manage physical infrastructure.

### Key Concepts
- **Logical Server**: Hosting point for one or more databases
- **DTU / vCore Model**: Determines pricing/performance
- **SQL Authentication**: Username/password access model

### What You Do in the Lab
- Create a SQL database with a logical server
- Set compute and storage options
- Connect using `sqlcmd` or SSMS

---

## 🔐 Firewall Rules

### Description
Control access to the database server by defining which IPs are allowed to connect.

### What You Do in the Lab
- Add a rule to allow your current IP address
- (Optional) Allow Azure services to access DB

---

## 🔄 Backup & High Availability (Managed)

### Description
Azure SQL includes automatic backups and HA by default.

### Key Concepts
- **Geo-redundancy**: Optional for backups and failover
- **Automated Backups**: Up to 35-day retention (configurable)

### What You Do in the Lab
- View backup settings (defaults)
- Explore restore options

---

## ✅ Summary

| Resource              | Purpose                                          | Created Using    |
|-----------------------|--------------------------------------------------|------------------|
| Azure SQL Database    | Managed relational DB hosting                    | Portal & CLI     |
| Logical Server        | Hosts and secures database access                | Portal           |
| Firewall Rule         | Limits external network access                   | Portal           |
| Backup & HA           | Ensures durability and minimal downtime          | Built-in         |

This lab prepares you to use Azure SQL for production-ready, secure, and reliable data storage without managing the underlying systems.

