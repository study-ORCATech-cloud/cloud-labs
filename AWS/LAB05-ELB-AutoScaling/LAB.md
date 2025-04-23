# LAB05: Deploy Load Balancer with Auto Scaling (AWS Console & CLI)

## ⚖️ Lab Overview

In this lab, you'll build a **highly available, scalable application infrastructure** using an **Application Load Balancer (ALB)** and an **Auto Scaling Group (ASG)**. You'll deploy a launch template, configure scaling policies, and route traffic using the ALB.

---

## 🎯 Objectives

- Create a launch template with EC2 configuration
- Deploy an Auto Scaling Group across multiple AZs
- Create an Application Load Balancer (ALB)
- Attach the ASG to the ALB target group
- Test traffic distribution and scaling

---

## 🛠️ Prerequisites

- AWS account
- IAM permissions for EC2, ELB, ASG
- CLI tools installed and configured

---

## 🧱 Part 1: Launch Template

1. Go to **EC2 > Launch Templates** > Create template
2. Name: `lab05-template`
3. AMI: Amazon Linux 2
4. Instance type: `t2.micro`
5. Add user data (simple web server):
```bash
#!/bin/bash
yum install -y httpd
systemctl start httpd
systemctl enable httpd
echo "<h1>Deployed from Auto Scaling Group</h1>" > /var/www/html/index.html
```
6. Create a key pair and assign to the template (optional for SSH)

---

## 🚦 Part 2: Create Target Group & ALB

1. Go to **EC2 > Load Balancers** > Create Load Balancer > **Application Load Balancer**
2. Name: `lab05-alb`, internet-facing
3. Listeners: HTTP (port 80)
4. Target Group: `lab05-tg`, type: instance, protocol: HTTP
5. Configure health check path: `/`

---

## 📈 Part 3: Auto Scaling Group

1. Go to **EC2 > Auto Scaling Groups** > Create ASG
2. Name: `lab05-asg`
3. Launch template: `lab05-template`
4. Network: Choose VPC and subnets (2+ AZs)
5. Attach to target group: `lab05-tg`
6. Set desired capacity: 2 (min: 1, max: 3)

### Test the Load Balancer
- Use the DNS name of the ALB to load the page
- Refresh to see traffic hitting different instances

---

## 💻 Part 4: Optional CLI Sample
```bash
aws autoscaling create-launch-configuration --launch-configuration-name lab05-lc \
  --image-id <ami-id> --instance-type t2.micro \
  --user-data file://userdata.sh
```

---

## 🧪 Challenge

- Implement a scaling policy (e.g., scale out when CPU > 50%)
- Monitor instance lifecycle events

---

## 🧹 Cleanup
- Delete the ASG, launch template, target group, and ALB

---

## ✅ Summary

You’ve created a resilient web application infrastructure that automatically adjusts to load. This is a vital foundation for scalable microservice and production cloud deployments.

