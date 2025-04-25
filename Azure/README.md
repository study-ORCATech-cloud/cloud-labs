# Azure Cloud Labs

## 🌩️ Welcome to Azure Labs

This collection of hands-on Azure labs is designed to build your practical skills using both the Azure Portal and the Azure Command Line Interface (CLI). Each lab focuses on core Azure services and follows a structured approach to help you understand cloud concepts through direct experience.

## 📋 Prerequisites

Before starting these labs, ensure you have:

1. **Azure Account**: Create a [free Azure account](https://azure.microsoft.com/en-us/free/)
2. **Azure CLI**: Install and configure the Azure CLI using our [CLI Setup Guide](../CLI-Setup.md)
3. **Basic Knowledge**:
   - Familiarity with command-line operations
   - Understanding of basic networking concepts
   - Comfort with text editors for configuration files

## 🔑 Authentication Setup

For these labs, we recommend these authentication options:

1. **Interactive Login** (Simplest for beginners):
   ```bash
   az login
   ```
   This opens a browser window for authentication.

2. **Service Principal** (For automation scenarios):
   - Create a service principal with contributor role
   - Use the credentials for non-interactive authentication
   - Details covered in [LAB03-AzureAD-Roles-Users](./LAB03-AzureAD-Roles-Users/)

⚠️ **Security Note**: Follow the principle of least privilege in production environments. The broader permissions suggested here are for learning purposes only.

## 🗺️ Labs Overview

| Lab | Service Focus | Description | Skills Learned |
|-----|---------------|-------------|----------------|
| [LAB01-Virtual-Machine](./LAB01-Virtual-Machine/) | Azure VM | Deploy and manage virtual machines | VM configuration, SSH/RDP access, disk management |
| [LAB02-Storage-Account](./LAB02-Storage-Account/) | Azure Storage | Create and configure storage accounts | Blob storage, file shares, access keys |
| [LAB03-AzureAD-Roles-Users](./LAB03-AzureAD-Roles-Users/) | Azure AD | Manage identities and access | User management, role assignments, RBAC |
| [LAB04-VNet-NSG-Peering](./LAB04-VNet-NSG-Peering/) | Azure Networking | Build virtual networks with security | VNets, subnets, NSGs, peering |
| [LAB05-LoadBalancer-ScaleSet](./LAB05-LoadBalancer-ScaleSet/) | Azure Scaling | Create resilient application infrastructure | Load balancers, VMSS, availability |
| [LAB06-Monitor-LogAnalytics](./LAB06-Monitor-LogAnalytics/) | Azure Monitor | Set up monitoring and logging | Metrics, logs, alerts, dashboards |
| [LAB07-SQL-Database](./LAB07-SQL-Database/) | Azure SQL | Deploy managed relational databases | Database configuration, security, backups |
| [LAB08-Blob-Lifecycle-Policies](./LAB08-Blob-Lifecycle-Policies/) | Azure Storage | Configure advanced storage features | Lifecycle management, access tiers, policies |
| [LAB09-AzureDNS-CustomDomain](./LAB09-AzureDNS-CustomDomain/) | Azure DNS | Set up DNS zones and records | DNS management, domain configuration |
| [LAB10-Functions-HTTPTrigger](./LAB10-Functions-HTTPTrigger/) | Azure Functions | Build serverless applications | Function Apps, HTTP triggers, integrations |

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
3. **Portal Instructions**: Step-by-step UI-based guidance
4. **CLI Instructions**: Command-line alternatives for each step
5. **TODO Tasks**: Hands-on exercises to complete
6. **Validation**: How to verify your implementation
7. **Cleanup**: Instructions to remove resources and avoid charges

## 💰 Cost Management

Most labs can be completed within the Azure Free Tier limits if you:

- Delete resource groups after completing each lab
- Utilize B-series VMs for cost optimization
- Monitor your cost analysis dashboard
- Set up budget alerts (covered in LAB06)

## 🔍 Troubleshooting

If you encounter issues:

1. Check the Azure service status page
2. Verify your permissions and role assignments
3. Ensure your subscription has available quotas
4. Review NSG rules for connectivity problems
5. Check Azure Activity Logs for error information

## 📚 Additional Resources

- [Azure Documentation](https://docs.microsoft.com/en-us/azure/)
- [Azure Architecture Center](https://docs.microsoft.com/en-us/azure/architecture/)
- [Azure Well-Architected Framework](https://docs.microsoft.com/en-us/azure/architecture/framework/)
- [Azure CLI Command Reference](https://docs.microsoft.com/en-us/cli/azure/reference-index)

## ✅ Ready to Begin?

Start with [LAB01-Virtual-Machine](./LAB01-Virtual-Machine/) to deploy your first Azure virtual machine!

Happy cloud computing! ☁️ 