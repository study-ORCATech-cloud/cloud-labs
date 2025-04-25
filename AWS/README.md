# AWS Cloud Labs

## 🌩️ Welcome to AWS Labs

This collection of hands-on AWS labs is designed to build your practical skills using both the AWS Management Console and the AWS Command Line Interface (CLI). Each lab focuses on core AWS services and follows a structured approach to help you understand cloud concepts through direct experience.

## 📋 Prerequisites

Before starting these labs, ensure you have:

1. **AWS Account**: Create a [free tier AWS account](https://aws.amazon.com/free/)
2. **AWS CLI**: Install and configure the AWS CLI using our [CLI Setup Guide](../CLI-Setup.md)
3. **Basic Knowledge**:
   - Familiarity with command-line operations
   - Understanding of basic networking concepts
   - Comfort with text editors for configuration files

## 🔑 Authentication Setup

For these labs, we recommend creating an IAM user with programmatic access:

1. Sign in to the AWS Management Console
2. Navigate to IAM service
3. Create a new user with programmatic access
4. Attach the `AdministratorAccess` policy (for learning purposes only)
5. Save the access key ID and secret access key
6. Configure your AWS CLI with `aws configure`

⚠️ **Security Note**: For production environments, always follow the principle of least privilege. The administrator access suggested here is for learning purposes only.

## 🗺️ Labs Overview

| Lab | Service Focus | Description | Skills Learned |
|-----|---------------|-------------|----------------|
| [LAB01-EC2-Instance](./LAB01-EC2-Instance/) | Amazon EC2 | Launch and configure a virtual server | Instance management, SSH access, security groups |
| [LAB02-S3-Bucket-Policy](./LAB02-S3-Bucket-Policy/) | Amazon S3 | Create storage buckets with policies | Object storage, bucket policies, permissions |
| [LAB03-IAM-Users-Groups](./LAB03-IAM-Users-Groups/) | AWS IAM | Manage users, groups and permissions | Identity management, policy creation, security |
| [LAB04-VPC-Networking](./LAB04-VPC-Networking/) | Amazon VPC | Design a custom virtual network | Subnets, routing, network ACLs, security |
| [LAB05-ELB-AutoScaling](./LAB05-ELB-AutoScaling/) | ELB & Auto Scaling | Create scalable application architecture | Load balancing, auto scaling, high availability |
| [LAB06-CloudWatch-Monitoring](./LAB06-CloudWatch-Monitoring/) | CloudWatch | Monitor resources and create alarms | Metrics, logs, dashboards, notifications |
| [LAB07-RDS-MySQL](./LAB07-RDS-MySQL/) | Amazon RDS | Deploy and configure a managed database | Database management, backups, security |
| [LAB08-S3-Lifecycle-Versioning](./LAB08-S3-Lifecycle-Versioning/) | Amazon S3 | Configure advanced S3 features | Versioning, lifecycle policies, storage classes |
| [LAB09-Route53-DNS](./LAB09-Route53-DNS/) | Route 53 | Set up DNS for your resources | Domain registration, record sets, routing policies |
| [LAB10-Lambda-API-Gateway](./LAB10-Lambda-API-Gateway/) | Lambda & API Gateway | Build serverless APIs | Serverless computing, API development, integrations |

## 🚶 Learning Path

These labs are designed to be completed sequentially, as each builds upon skills from previous labs:

1. **Foundation** (Labs 1-3): Learn core compute, storage, and security concepts
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

Most labs can be completed within the AWS Free Tier limits if you:

- Terminate EC2 instances after each lab
- Delete non-essential resources when finished
- Monitor your billing dashboard regularly
- Set up billing alerts (covered in LAB06)

## 🔍 Troubleshooting

If you encounter issues:

1. Check the AWS service status page
2. Verify your IAM permissions
3. Ensure your region is set correctly
4. Review security group rules for connectivity problems
5. Check the AWS documentation for service-specific troubleshooting

## 📚 Additional Resources

- [AWS Documentation](https://docs.aws.amazon.com/)
- [AWS Architecture Center](https://aws.amazon.com/architecture/)
- [AWS Well-Architected Framework](https://aws.amazon.com/architecture/well-architected/)
- [AWS CLI Command Reference](https://awscli.amazonaws.com/v2/documentation/api/latest/index.html)

## ✅ Ready to Begin?

Start with [LAB01-EC2-Instance](./LAB01-EC2-Instance/) to launch your first AWS virtual server!

Happy cloud computing! ☁️ 