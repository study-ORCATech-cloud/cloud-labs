# AWS LAB05: Resource Overview – Load Balancer and Auto Scaling Group

## ⚖️ What Resources Are We Working With?

In **AWS LAB05**, you’ll learn how to build highly available and scalable infrastructure using **Elastic Load Balancers (ELB)** and **Auto Scaling Groups (ASG)**. These services help distribute traffic and ensure your application can handle changing loads with minimal manual effort.

---

## 🚦 Elastic Load Balancer (ELB)

### Description
The **Application Load Balancer (ALB)** distributes incoming application traffic across multiple targets, such as EC2 instances.

### Key Concepts
- **Listeners**: Define ports and protocols (e.g., HTTP on port 80)
- **Target Groups**: Group of instances to forward requests to
- **Health Checks**: Monitor target health and route traffic accordingly

### What You Do in the Lab
- Create an ALB and a target group
- Register EC2 instances as targets
- Configure health checks

---

## 📈 Auto Scaling Group (ASG)

### Description
An ASG automatically adjusts the number of EC2 instances in response to demand.

### Key Concepts
- **Launch Template**: Defines how to launch instances (AMI, size, security groups)
- **Scaling Policies**: Rules for when to add/remove instances
- **Desired, Min, Max Capacity**: Controls instance counts

### What You Do in the Lab
- Create a launch template
- Create an ASG using that template
- Attach the ASG to the target group of your ALB
- Test autoscaling behavior by adjusting desired capacity

---

## 📦 EC2 Instances

### Description
Used as the backend compute layer behind the load balancer.

### What You Do in the Lab
- Launch test instances via ASG
- Connect via SSH to verify behavior

---

## ✅ Summary

| Resource            | Purpose                                           | Created Using    |
|---------------------|--------------------------------------------------|------------------|
| Application Load Balancer | Distributes traffic across EC2 targets        | Console & CLI    |
| Target Group        | Logical collection of EC2 targets                 | Console & CLI    |
| Auto Scaling Group  | Dynamically manages number of EC2 instances      | Console & CLI    |
| Launch Template     | Blueprint for EC2 instance creation              | Console & CLI    |

This lab builds foundational skills for designing cloud-native, resilient, and scalable applications.