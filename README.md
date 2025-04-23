# Cloud Labs (UI & CLI) Repository

A curated, hands-on collection of beginner-to-advanced labs that teach cloud skills using **AWS**, **Azure**, and **GCP** — via **web consoles and official CLI tools**. These labs are designed to build strong foundational knowledge **without using Infrastructure as Code tools** like Terraform.

---

## 🌐 About This Repository

This project is ideal for learners who want to understand how cloud platforms work at a hands-on level — provisioning, configuring, and managing real resources directly through the UI and CLI. It’s the perfect step before diving into automation and DevOps tooling.

---

## 📁 Repository Structure

```bash
cloud-labs/
│
├── AWS/                      # AWS UI & CLI labs
│   ├── LAB01-EC2-Instance/
│   ├── LAB02-S3-Bucket/
│   └── ...
│
├── Azure/                    # Azure UI & CLI labs
│   ├── LAB01-Virtual-Machine/
│   ├── LAB02-Storage-Account/
│   └── ...
│
├── GCP/                      # GCP UI & CLI labs
│   ├── LAB01-Compute-Engine/
│   ├── LAB02-Cloud-Storage/
│   └── ...
│
└── Common/                   # Shared documentation and utilities
    └── CLI-Setup.md          # Instructions for setting up CLI tools
```

---

## 🚀 Getting Started

### Prerequisites

To complete the labs, you'll need:
- An account with **AWS**, **Azure**, or **GCP**
- CLI tools installed and authenticated:
  - [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html)
  - [Azure CLI](https://learn.microsoft.com/en-us/cli/azure/install-azure-cli)
  - [Google Cloud SDK](https://cloud.google.com/sdk/docs/install)

> CLI setup instructions are provided in [`Common/CLI-Setup.md`](./Common/CLI-Setup.md)

---

## 🧪 How to Use These Labs

1. Clone the repository:
   ```bash
   git clone https://github.com/<your-org>/cloud-labs.git
   cd cloud-labs
   ```

2. Navigate to the cloud provider folder of your choice.
3. Choose a lab by its topic or number.
4. Open the `LAB.md` file and follow the instructions step by step.
5. Try the optional challenges to push your learning further!

Each lab includes:
- A **resource overview**
- **UI + CLI instructions**
- **Challenges** to deepen understanding
- **Troubleshooting tips**

---

## 📈 Learning Progression

Labs are designed to gradually increase in difficulty:

- **Beginner**: Launch VMs, create buckets, understand IAM basics
- **Intermediate**: Configure networks, secure access, monitor usage
- **Advanced**: Automate logic, scale workloads, handle multi-region architecture

---

## 👥 Who Should Use This?

- Cloud beginners exploring AWS, Azure, or GCP
- DevOps learners building foundational experience
- Developers looking to understand platform services before automation

---

## 🗺️ Roadmap & Lab Index

Looking for the full list of labs and what’s coming next?

👉 Check the full [ROADMAP.md](./ROADMAP.md)

---

## 🤝 Contributing

We welcome contributions of all kinds:

1. Fork the repo
2. Create a branch (`feature/lab-new-topic`)
3. Add your lab in the correct directory
4. Submit a PR with a detailed explanation

You can also improve documentation, diagrams, or submit issues!

---

## 🙏 Acknowledgments

- Official AWS, Azure, and GCP docs
- Community-driven examples
- DevOps engineers, educators, and cloud advocates who inspired this project

---

## 🌟 Happy Learning!

Build confidence. Gain experience. Master the cloud — one lab at a time.

