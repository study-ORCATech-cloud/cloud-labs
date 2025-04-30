# AWS LAB15: Resource Overview – AWS Elastic Beanstalk

## 🗄️ What Resources Are We Working With?

In **AWS LAB15**, you'll explore **application deployment and management** using **AWS Elastic Beanstalk**. This lab teaches you how to quickly deploy and manage web applications without worrying about the infrastructure that runs those applications. Elastic Beanstalk reduces management complexity without restricting choice or control, allowing you to focus on writing code rather than managing infrastructure.

---

## 📊 AWS Elastic Beanstalk

### Description
Elastic Beanstalk is an easy-to-use service for deploying and scaling web applications and services. You can simply upload your code, and Elastic Beanstalk automatically handles the deployment, from capacity provisioning, load balancing, and auto-scaling to application health monitoring.

### Key Concepts
- **Applications**: Logical collection of Elastic Beanstalk components, including environments, versions, and environment configurations
- **Application Versions**: Specific labeled iterations of deployable code for an application
- **Environments**: Collection of AWS resources running an application version
- **Environment Tiers**: Determines whether Elastic Beanstalk provisions resources to support a web application or web service
- **Platform**: Combination of OS, programming language runtime, web server, application server, and Beanstalk components
- **Environment Configurations**: Collection of parameters and settings that define how an environment and its resources behave

### What You Do in the Lab
- Create an Elastic Beanstalk application
- Configure and deploy a sample application
- Manage environment settings and configurations
- Implement application updates and versioning
- Monitor application health and performance
- Scale the application resources up and down

---

## 🔎 Environment Tiers

### Description
Elastic Beanstalk provides two environment tiers that determine the type of application that can be deployed and the AWS resources that are provisioned to support it.

### Key Concepts
- **Web Server Environment**: Standard architecture for processing web requests, with an Auto Scaling group, ELB load balancer, and other supporting resources
- **Worker Environment**: Architecture for background processing tasks, includes SQS queue, Auto Scaling group, and IAM service role

### What You Do in the Lab
- Create a web server environment for hosting a web application
- Configure environment settings for the application
- Understand how each tier processes requests and background tasks
- Monitor environment health and resource utilization

---

## 🔍 Deployment Options

### Description
Elastic Beanstalk provides several deployment methods and configuration options to control how your application versions are deployed.

### Key Concepts
- **Deployment Policies**: All-at-once, Rolling, Rolling with additional batch, Immutable, and Blue/Green
- **Deployment Options**: In-place updates vs. Blue/Green deployments
- **Application Source Bundle**: The code package (zip or war file) that you upload to Elastic Beanstalk
- **Environment Manifest**: YAML configuration file for environment creation and updates

### What You Do in the Lab
- Package a sample application for deployment
- Implement different deployment policies
- Update application versions using different methods
- Configure environment variables and settings
- Learn best practices for continuous deployment

---

## 🔄 Scaling and Monitoring

### Description
Elastic Beanstalk provides tools for automatically scaling your application and monitoring its health and performance.

### Key Concepts
- **Auto Scaling**: Automatically adjusts capacity based on application demand
- **Health Monitoring**: Tracks environment health status and key metrics
- **Enhanced Health Reporting**: Provides additional health information beyond basic checks
- **CloudWatch Integration**: Monitors and collects metrics about application resources
- **Managed Updates**: Automatically applies platform updates to keep environments up-to-date

### What You Do in the Lab
- Configure Auto Scaling settings
- Set up health monitoring and enhanced health reporting
- Create alarms for critical performance thresholds
- Review logs and metrics in the Elastic Beanstalk console
- Implement instance profile and service roles

---

## ✅ Summary

| Resource                    | Purpose                                             | Created Using    |
|-----------------------------|-----------------------------------------------------|------------------|
| Elastic Beanstalk Application| Logical container for application components        | Console & CLI    |
| Application Versions        | Specific iterations of deployable code              | Console & CLI    |
| Environments                | AWS resources running an application version        | Console & CLI    |
| Environment Configurations  | Settings that define environment behavior           | Console & CLI    |
| Auto Scaling Group          | Manages EC2 instances for the environment           | Console & CLI    |
| Load Balancer               | Distributes traffic to environment instances        | Console & CLI    |
| CloudWatch Alarms           | Monitors environment health and metrics             | Console & CLI    |

This lab provides hands-on experience with AWS Elastic Beanstalk, teaching you how to quickly deploy and manage applications in the AWS Cloud without needing to worry about the infrastructure. You'll learn how to leverage Elastic Beanstalk for continuous deployment, capacity provisioning, load balancing, auto-scaling, and health monitoring. 