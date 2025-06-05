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

Screenshot of the container running, docker build and docker run commands
![Docker Build](https://raw.githubusercontent.com/vishcloud/CloudDrove_Assignment/main/Screenshots/docker-build.png)



