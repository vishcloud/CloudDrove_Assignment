# 🚀 Azure Infrastructure Deployment via Dockerized Terraform Environment

## 🧠 Objective

Create a **portable Docker environment** to provision infrastructure on **Microsoft Azure** using **Terraform**, with minimal local dependencies. This containerized setup enables clean, repeatable, and secure infrastructure provisioning from any machine.

---

## 🔧 Tools Used

- Docker
- Terraform
- Azure CLI
- Bash
- Azure Subscription

---

## 📁 Project Structure

├── Dockerfile # Defines the custom image with Terraform and Azure CLI

├── main.tf # Terraform configuration file

└── README.md # Documentation (this file)

---

## 📝 Approach (with Inline Documentation)

The solution is structured into four key tasks, each with inline explanations and best practices.

---

### ✅ **Task 1: Docker Image Preparation**

> 📄 Dockerfile defines a custom image based on Ubuntu that installs required tools, Azure CLI, and Terraform.

**Dockerfile:**
```Dockerfile
# Base image
FROM ubuntu:latest

# Install dependencies
RUN apt-get update && apt-get install -y \
    curl \
    unzip \
    gnupg \
    software-properties-common \
    apt-transport-https \
    lsb-release

# Install Terraform
RUN curl -fsSL https://apt.releases.hashicorp.com/gpg | gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg && \
    echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" > /etc/apt/sources.list.d/hashicorp.list && \
    apt-get update && apt-get install -y terraform

# Install Azure CLI
RUN curl -sL https://aka.ms/InstallAzureCLIDeb | bash

# Set working directory
WORKDIR /workspace

# Auto-run Terraform if .tf files are detected (inline automation)
CMD ["/bin/bash"]
✅ Task 2: Build & Run Docker Container

# Build the custom image
docker build -t terraform-azure-env:latest .

# Run the container interactively and mount local workspace
docker run -it --rm -v $(pwd):/workspace terraform-azure-env
📌 Inline Explanation:

-v $(pwd):/workspace mounts current directory to container's /workspace

--rm ensures clean up after container exits

Working directory /workspace contains main.tf

✅ Task 3: Terraform Deployment (Inside Container)
1. Login to Azure:

az login
2. main.tf (Terraform file to deploy a Resource Group):

provider "azurerm" {
  features {}
  use_azuread_auth = true  # Use Azure CLI credentials
}

resource "azurerm_resource_group" "example" {
  name     = "devops-test-rg"
  location = "eastus"
}
3. Initialize Terraform:

terraform init
4. Plan and Apply Deployment:

terraform plan
terraform apply
📝 Output:

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
🖼 Screenshot: Azure Portal shows devops-test-rg created in eastus.

✅ Task 4: Automation & Cleanup
🔁 Modify Dockerfile to auto-run Terraform if .tf files exist:

Dockerfile CMD Improvement:

CMD ["bash", "-c", "if ls /workspace/*.tf 1> /dev/null 2>&1; then terraform init && terraform apply -auto-approve; else exec bash; fi"]
To destroy the infrastructure (inside container):

terraform destroy
📦 Deliverables
File	Description
Dockerfile	Defines a portable Terraform + Azure CLI environment
main.tf	Declares Azure resource group
variables.tf 
README.md	Documentation of approach
Screenshots	Running container, Docker build/run output, Terraform apply, Azure Portal

✅ Learnings & Best Practices
✅ Use Docker to eliminate host setup dependencies

✅ Use az login for quick, interactive Terraform auth

✅ Use service principals for automation (optional extension)

✅ Mount local volumes to retain .tf files

✅ Cleanup resources with terraform destroy

🔐 Security Tip
For automation, avoid az login and instead:

Use Azure service principals (az ad sp create-for-rbac)

Pass secrets securely using environment variables

👋 Author
Vishal Pimpale
DevOps Engineer
GitHub: https://github.com/vishcloud



