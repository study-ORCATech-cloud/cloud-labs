# Google Cloud Platform Labs

## 🌩️ Welcome to GCP Labs

This collection of hands-on Google Cloud Platform (GCP) labs is designed to build your practical skills using both the Google Cloud Console and the Google Cloud SDK (gcloud CLI). Each lab focuses on core GCP services and follows a structured approach to help you understand cloud concepts through direct experience.

## 📋 Prerequisites

Before starting these labs, ensure you have:

1. **GCP Account**: Create a [Google Cloud free tier account](https://cloud.google.com/free)
2. **Google Cloud SDK**: Install and configure the gcloud CLI using our [CLI Setup Guide](../CLI-Setup.md)
3. **Basic Knowledge**:
   - Familiarity with command-line operations
   - Understanding of basic networking concepts
   - Comfort with text editors for configuration files

## 🔑 Authentication Setup

For these labs, we recommend these authentication options:

1. **Interactive Login** (Simplest for beginners):
   ```bash
   gcloud auth login
   ```
   This opens a browser window for authentication.

2. **Service Account** (For automation scenarios):
   ```bash
   # After creating a service account key file
   gcloud auth activate-service-account --key-file=your-key-file.json
   ```

3. **Set Default Project**:
   ```bash
   gcloud config set project your-project-id
   ```

⚠️ **Security Note**: Follow the principle of least privilege in production environments. The broader permissions suggested here are for learning purposes only.

## 🗺️ Labs Overview

| Lab | Service Focus | Description | Skills Learned |
|-----|---------------|-------------|----------------|
| [LAB01-Compute-Engine](./LAB01-Compute-Engine/) | Compute Engine | Deploy and manage virtual machines | VM configuration, SSH access, persistent disks |
| [LAB02-Cloud-Storage-Bucket](./LAB02-Cloud-Storage-Bucket/) | Cloud Storage | Create and configure storage buckets | Object storage, access control, permissions |
| [LAB03-IAM-Roles-ServiceAccounts](./LAB03-IAM-Roles-ServiceAccounts/) | IAM | Manage identity and access | Role binding, service accounts, permissions |
| [LAB04-VPC-Firewall-Rules](./LAB04-VPC-Firewall-Rules/) | VPC | Build virtual networks with security | VPC networks, subnets, firewall rules |
| [LAB05-LoadBalancer-InstanceGroup](./LAB05-LoadBalancer-InstanceGroup/) | Load Balancing | Create resilient application infrastructure | Instance groups, health checks, load balancing |
| [LAB06-CloudMonitoring-Logging](./LAB06-CloudMonitoring-Logging/) | Operations Suite | Set up monitoring and logging | Metrics, logs, dashboards, alerts |
| [LAB07-CloudSQL-PostgreSQL](./LAB07-CloudSQL-PostgreSQL/) | Cloud SQL | Deploy managed relational databases | Database configuration, backups, security |
| [LAB08-Storage-Lifecycle-Retention](./LAB08-Storage-Lifecycle-Retention/) | Cloud Storage | Configure advanced storage features | Lifecycle policies, object versioning, retention |
| [LAB09-CloudDNS-CustomDomain](./LAB09-CloudDNS-CustomDomain/) | Cloud DNS | Set up DNS zones and records | DNS management, domain configuration |
| [LAB10-CloudFunctions-HTTP](./LAB10-CloudFunctions-HTTP/) | Cloud Functions | Build serverless applications | Functions, HTTP triggers, integrations |

## 🚶 Learning Path

These labs are designed to be completed sequentially, as each builds upon skills from previous labs:

1. **Foundation** (Labs 1-3): Learn core compute, storage, and identity concepts
2. **Networking** (Labs 4-5): Build and scale application infrastructures
3. **Operations** (Labs 6-8): Master monitoring, databases, and advanced storage
4. **Advanced Services** (Labs 9-10): Implement DNS and serverless architectures

## 📝 Lab Structure

Each lab follows a consistent format:

1. **Overview**: Introduction and learning objectives
2. **Architecture Diagram**: Visual representation of resources
3. **Console Instructions**: Step-by-step UI-based guidance
4. **CLI Instructions**: Command-line alternatives for each step
5. **TODO Tasks**: Hands-on exercises to complete
6. **Validation**: How to verify your implementation
7. **Cleanup**: Instructions to remove resources and avoid charges

## 💰 Cost Management

Most labs can be completed within the Google Cloud Free Tier limits if you:

- Delete Compute Engine instances when not in use
- Use e2-micro instances when possible (included in free tier)
- Monitor your billing dashboard regularly
- Set up billing budget alerts (covered in LAB06)
- Stay within free tier limits for Cloud Storage and other services

## 🔍 Troubleshooting

If you encounter issues:

1. Check the [Google Cloud Status Dashboard](https://status.cloud.google.com/)
2. Verify your IAM permissions and role bindings
3. Ensure APIs are enabled for the services you're using
4. Review VPC firewall rules for connectivity problems
5. Check Google Cloud Logging for error information

## 📚 Additional Resources

- [Google Cloud Documentation](https://cloud.google.com/docs)
- [Google Cloud Architecture Center](https://cloud.google.com/architecture)
- [Google Cloud SDK Reference](https://cloud.google.com/sdk/gcloud/reference)
- [Google Cloud Codelabs](https://codelabs.developers.google.com/?cat=cloud)

## ✅ Ready to Begin?

Start with [LAB01-Compute-Engine](./LAB01-Compute-Engine/) to deploy your first Google Cloud virtual machine!

Happy cloud computing! ☁️ 