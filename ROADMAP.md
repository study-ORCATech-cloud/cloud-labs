# Cloud Labs: Cross-Platform Hands-On Learning Roadmap

Welcome to the **Cloud Labs** project — a curated set of practical labs designed to teach foundational and advanced cloud skills using **AWS**, **Azure**, and **GCP**. This repository helps learners gain hands-on experience with both **manual provisioning** (via UI and CLI) and **infrastructure fundamentals**.

---

## 📅 Learning Roadmap

The labs are organized by cloud provider and progress from basic to advanced tasks.

### ✅ Completed Labs

#### AWS Labs
| Lab   | Title                             | Description                                            |
|--------|----------------------------------|--------------------------------------------------------|
| LAB01 | EC2 Instance                      | Launch a VM via AWS Console & CLI                      |
| LAB02 | S3 Bucket                         | Manage object storage & website hosting                |
| LAB03 | IAM Roles and Policies            | Create IAM users, roles, and apply least privilege     |
| LAB04 | VPC and Security Groups           | Build custom VPC, subnets, NSGs                        |
| LAB05 | Load Balancer + Auto Scaling      | Implement scalability and high availability            |
| LAB06 | CloudWatch Logs and Alarms        | Monitor logs and metrics, create alerts                |
| LAB07 | RDS MySQL                         | Deploy a managed database using Amazon RDS             |
| LAB08 | S3 Lifecycle + Versioning         | Automate retention, optimize storage costs             |
| LAB09 | Route 53 + Custom Domain          | Set up DNS records and domain routing                  |
| LAB10 | Lambda + API Gateway              | Deploy serverless functions with RESTful APIs          |

#### Azure Labs
| Lab   | Title                             | Description                                            |
|--------|----------------------------------|--------------------------------------------------------|
| LAB01 | Virtual Machine                   | Deploy a VM with portal & CLI                          |
| LAB02 | Blob Storage                      | Store & serve data, enable website                     |
| LAB03 | Azure AD + RBAC                   | Manage users, groups, and role assignments             |
| LAB04 | Virtual Network + NSGs            | Build secure networking with subnet peering            |
| LAB05 | Load Balancer + VM Scale Set      | Implement load balancing and auto-scaling              |
| LAB06 | Monitor + Log Analytics           | Monitor and alert on system metrics                    |
| LAB07 | Azure SQL Database                | Create and query managed relational DB                 |
| LAB08 | Blob Lifecycle Management         | Automate archival with lifecycle rules                 |
| LAB09 | Azure DNS + Custom Domain         | Set up DNS routing for custom domains                  |
| LAB10 | Azure Functions + HTTP Trigger    | Deploy serverless functions                            |

#### GCP Labs
| Lab   | Title                             | Description                                            |
|--------|----------------------------------|--------------------------------------------------------|
| LAB01 | Compute Engine VM                 | Deploy and access a VM                                 |
| LAB02 | Cloud Storage Bucket              | Upload data and host static website                    |
| LAB03 | IAM Roles & Service Accounts      | Secure access using roles and service accounts         |
| LAB04 | VPC + Firewall Rules              | Design custom networks and control traffic             |
| LAB05 | Load Balancer + Instance Group    | Distribute traffic and autoscale VMs                   |
| LAB06 | Monitoring + Logging              | Visualize and alert on logs and metrics                |
| LAB07 | Cloud SQL PostgreSQL              | Deploy a managed relational database                   |
| LAB08 | Object Lifecycle + Retention      | Automate data retention and lifecycle policies         |
| LAB09 | Cloud DNS + Custom Domain         | Manage DNS records and connect custom domains          |
| LAB10 | Cloud Functions + HTTP Trigger    | Build and expose a serverless API                      |

---

### ⏳ Planned Labs (Coming Soon)

#### AWS
| Lab   | Title                             | Description                                            |
|--------|----------------------------------|--------------------------------------------------------|
| LAB11 | CloudFront Static Site            | Deliver website content via CDN                        |
| LAB12 | Athena Querying                   | Query S3 data using SQL with Athena                    |
| LAB13 | ECS Fargate Deployment            | Run containers without managing servers                |

#### Azure
| Lab   | Title                             | Description                                            |
|--------|----------------------------------|--------------------------------------------------------|
| LAB11 | Azure Front Door                  | Global routing and caching for web apps                |
| LAB12 | Key Vault + Secret Access         | Manage secrets securely                                |
| LAB13 | AKS Kubernetes Deployment         | Container orchestration with Azure Kubernetes Service  |

#### GCP
| Lab   | Title                             | Description                                            |
|--------|----------------------------------|--------------------------------------------------------|
| LAB11 | Cloud Run                         | Run containers in a fully managed environment          |
| LAB12 | BigQuery Analytics                | Run SQL queries over large datasets                    |
| LAB13 | GKE Kubernetes Deployment         | Deploy and scale containers with GKE                   |

---

## 🎓 How to Use This Repository

Each lab folder contains:
- `LAB.md` with full instructions for Console and CLI usage
- A separate `README.md` file explaining the cloud resources involved
- Optional challenges for deeper learning

> You are encouraged to follow the labs in order and complete the optional exercises.

---

## 🚀 Contribution Guide

Want to contribute?
1. Fork this repo
2. Create a new folder following the existing naming structure (e.g., `AWS/LAB03-IAM`)
3. Include both lab instructions and a resource explanation
4. Submit a pull request with a clear summary

We welcome new labs, corrections, diagrams, and learning challenges!

---

## 🔗 Related Projects
- [Terraform Labs Repository](../terraform-labs): Automate infrastructure provisioning after completing manual UI/CLI labs.

---

## 🎉 Happy Learning!

Master the cloud, one lab at a time. This project is built to give you real-world experience and the confidence to work across multiple cloud platforms.