# AWS LAB17: Resource Overview – AWS CloudFormation

## 🗄️ What Resources Are We Working With?

In **AWS LAB17**, you'll explore **infrastructure as code** using **AWS CloudFormation**. This lab teaches you how to define, deploy, and manage AWS infrastructure using template files, enabling repeatable, automated provisioning of resources while maintaining version control and consistency.

---

## 📊 AWS CloudFormation

### Description
CloudFormation is an infrastructure as code (IaC) service that enables you to model, provision, and manage AWS and third-party resources by treating infrastructure as code. It uses templates to create and manage a collection of related AWS resources, automating their provisioning and configuration.

### Key Concepts
- **Templates**: JSON or YAML files that describe the AWS resources and their properties
- **Stacks**: A collection of AWS resources that you can manage as a single unit
- **Stack Sets**: Extend stacks across multiple accounts and regions
- **Change Sets**: Preview changes to existing stacks before implementing them
- **Parameters**: Values to pass to your template at stack creation time
- **Mappings**: Key-value mappings for specifying conditional parameter values
- **Resources**: AWS resources you want to create (EC2, S3, etc.)
- **Outputs**: Values that are returned after stack creation
- **Drift Detection**: Identify changes made outside of CloudFormation

### What You Do in the Lab
- Create CloudFormation templates in YAML and JSON formats
- Define parameters and resources in templates
- Deploy stacks using templates
- Update existing stacks
- Use change sets to preview stack modifications
- Create template parameters for dynamic input
- Define stack outputs for easy reference
- Implement nested stacks for modular architecture

---

## 🏗️ AWS CloudFormation Designer

### Description
CloudFormation Designer is a graphic tool for creating, viewing, and modifying CloudFormation templates using a drag-and-drop interface.

### Key Concepts
- **Visual Editor**: Drag-and-drop interface for building templates
- **Resource Relationships**: Visualization of connections between resources
- **Template Validation**: Built-in syntax validation
- **Integrated Experience**: Seamless transition between graphical and code views

### What You Do in the Lab
- Use CloudFormation Designer to create templates visually
- Understand resource relationships in a template
- Modify templates using the graphical interface
- Convert between visual representation and YAML/JSON

---

## 🛠️ AWS CLI for CloudFormation

### Description
The AWS Command Line Interface (CLI) provides commands for creating, updating, and managing CloudFormation stacks directly from the command line.

### Key Concepts
- **Stack Creation Commands**: Commands to create and deploy stacks
- **Stack Update Commands**: Commands to modify existing stacks
- **Stack Deletion Commands**: Commands to remove stacks
- **Stack Monitoring Commands**: Commands to check stack status

### What You Do in the Lab
- Use AWS CLI to deploy CloudFormation templates
- Validate templates before deployment
- Monitor stack creation and update progress
- List and describe stack resources using CLI

---

## ✅ Summary

| Resource                   | Purpose                                              | Created Using    |
|----------------------------|------------------------------------------------------|------------------|
| CloudFormation Templates   | Define infrastructure as code                        | Console & CLI    |
| CloudFormation Stacks      | Manage collections of AWS resources as a unit        | Console & CLI    |
| Template Parameters        | Customize template for different environments        | Console & CLI    |
| Change Sets                | Preview changes before implementation                | Console & CLI    |
| Stack Outputs              | Expose information from deployed stacks              | Console & CLI    |
| EC2 Instances (example)    | Compute resources defined in template                | Template         |
| VPC Network (example)      | Network resources defined in template                | Template         |
| S3 Bucket (example)        | Storage resources defined in template                | Template         |

This lab provides hands-on experience with AWS CloudFormation, teaching you how to automate the provisioning of infrastructure, maintain consistent environments, and manage resources at scale using infrastructure as code principles. 