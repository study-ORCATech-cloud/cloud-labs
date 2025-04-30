# AWS LAB18: Resource Overview – Amazon ECR and ECS Container Services

## 🗄️ What Resources Are We Working With?

In **AWS LAB18**, you'll explore **container deployment** using **Amazon Elastic Container Registry (ECR)** and **Amazon Elastic Container Service (ECS)**. This lab teaches you how to build, store, and deploy containerized applications in AWS, enabling scalable, reliable, and consistent deployments across environments.

---

## 📊 Amazon Elastic Container Registry (ECR)

### Description
Amazon ECR is a fully managed Docker container registry that makes it easy to store, manage, and deploy container images. ECR integrates with ECS, simplifying your development to production workflow, and eliminates the need to operate your own container repositories.

### Key Concepts
- **Repository**: A collection of related container images, usually of the same application
- **Image**: A read-only template with instructions for creating a container
- **Tag**: A label applied to an image to identify versions or variants
- **Lifecycle Policy**: Rules to automate the cleanup of unused images
- **Image Scanning**: Built-in vulnerability scanning for container images
- **Repository Permissions**: IAM-based access control for repositories

### What You Do in the Lab
- Create an ECR repository
- Authenticate your Docker client to the repository
- Build and tag a container image
- Push the image to ECR
- Pull images from ECR
- Set up lifecycle policies
- Configure repository permissions

---

## 🐳 Amazon Elastic Container Service (ECS)

### Description
Amazon ECS is a highly scalable, fast container management service that makes it easy to run, stop, and manage containers on a cluster. ECS lets you launch and stop container-based applications with simple API calls, allowing you to get the state of your cluster from a centralized service, and gives you access to many familiar Amazon EC2 features.

### Key Concepts
- **Cluster**: A logical grouping of tasks or services
- **Task Definition**: Blueprint for your application that defines containers, volumes, etc.
- **Task**: Instance of a task definition running on a container instance
- **Service**: Maintains a specified number of task instances
- **Container Instance**: EC2 instance running the ECS agent
- **Fargate**: Serverless compute engine for containers
- **Launch Type**: EC2 (self-managed) or Fargate (fully managed)

### What You Do in the Lab
- Create an ECS cluster
- Define task definitions
- Run tasks
- Create and update services
- Configure auto scaling
- Implement load balancing for containerized applications
- Monitor container deployments

---

## 🌉 Container Networking and Load Balancing

### Description
AWS provides several networking features to connect, secure, and distribute traffic across containers, enabling highly available applications.

### Key Concepts
- **Task Networking**: How containers communicate with each other and the outside world
- **Service Discovery**: Mechanism for containers to find and communicate with each other
- **Application Load Balancer**: Distributes incoming application traffic across containers
- **Security Groups**: Virtual firewalls that control traffic to and from containers
- **VPC**: Isolated network environment for your containers

### What You Do in the Lab
- Configure container networking settings
- Set up Application Load Balancer for container services
- Implement service discovery
- Secure container traffic with security groups
- Route traffic between containers across subnets

---

## 🔄 CI/CD Integration

### Description
One of the main benefits of containers is the ability to implement efficient CI/CD pipelines for faster, more reliable application delivery.

### Key Concepts
- **Image Building**: Automated creation of container images
- **Deployment Strategy**: How new container versions are rolled out
- **Blue/Green Deployment**: Technique for releasing applications by shifting traffic
- **Rolling Updates**: Gradually replacing instances of the previous version of an application

### What You Do in the Lab
- Set up automated image builds
- Configure deployment pipelines
- Implement rolling updates
- Test blue/green deployments with ECS services

---

## ✅ Summary

| Resource                      | Purpose                                              | Created Using    |
|-------------------------------|------------------------------------------------------|------------------|
| ECR Repository                | Store and manage container images                    | Console & CLI    |
| Docker Image                  | Packaged application with dependencies               | Docker CLI       |
| ECS Cluster                   | Logical grouping of container tasks                  | Console & CLI    |
| Task Definition               | Blueprint for running containerized applications     | Console & CLI    |
| ECS Service                   | Maintain specified number of task instances          | Console & CLI    |
| Application Load Balancer     | Distribute traffic to containers                     | Console & CLI    |
| Auto Scaling Configuration    | Automatically adjust container capacity              | Console & CLI    |
| IAM Roles                     | Provide necessary permissions to ECS tasks           | Console & CLI    |
| CloudWatch Logs               | Monitor container logs                               | Console & CLI    |

This lab provides hands-on experience with AWS container services, teaching you how to deploy, manage, and scale containerized applications in a production-like environment using industry best practices. 