# AWS EC2 & Terraform Assignment

## Table of Contents
- [Project Overview](#project-overview)
- [Environment Note](#environment-note-localstack-implementation)
- [Core Concepts](#core-concepts)
- [Manual Launch (AWS CLI)](#-part-1-manual-launch-aws-cli)
- [Terraform Launch (Automated)](#-part-2-terraform-launch-automated)
- [Technical Setup](#-technical-setup)

## 📋 Project Overview
This repository shows how to provision AWS EC2 instances using two methods:
1. **Manual Provisioning:** AWS CLI
2. **Infrastructure as Code (IaC):** Terraform

### ⚠️ Environment Note: LocalStack Implementation
I do not have an AWS account, so this assignment uses **LocalStack** to simulate AWS locally.

LocalStack is a cloud service emulator that runs in a Docker container. It simulates the AWS environment locally, allowing execution of AWS CLI commands and Terraform scripts without requiring a live cloud connection or credit card. This approach demonstrates the same logic and syntax required for real AWS environments.

---

## 📚 Core Concepts

### 1. AWS EC2 (Elastic Compute Cloud)
EC2 is a web service that provides secure, resizable compute capacity in the cloud. It is essentially a remote virtual computer that can be used to host applications.

### 2. Terraform
Terraform is an open-source "Infrastructure as Code" tool. It allows you to define cloud resources (like EC2 instances) in human-readable configuration files that can be versioned, reused, and shared.

---

## 🛠️ Part 1: Manual Launch (AWS CLI)

For the manual launch, I used the AWS CLI to issue an imperative command to the LocalStack endpoint. This simulates the action of "clicking buttons" in the console or running ad-hoc commands.

**Command Executed:**
```bash
aws --endpoint-url=http://localhost:4566 ec2 run-instances \
    --image-id ami-df5de72ade6b \
    --instance-type t2.micro \
    --tag-specifications 'ResourceType=instance,Tags=[{Key=Name,Value=Manual-Instance}]'
```

Proof of Execution:

<img src="manual.png" alt="Manual Proof" width="480px">

*Figure: Manual launch proof — EC2 instance created with AWS CLI against LocalStack.*

## 🚀 Part 2: Terraform Launch (Automated)

For the automated launch, I defined the infrastructure declaratively using a `main.tf` file. This ensures the infrastructure is reproducible.

Configuration (main.tf): The provider is configured to point to the LocalStack endpoint (http://localhost:4566) instead of the default AWS public API.

Commands Executed:

```bash
terraform init                  # Initialize the working directory
terraform apply -auto-approve   # Create the resources
```

Proof of Execution:

<img src="terraform.png" alt="Terraform Proof" width="480px">

*Figure: Terraform launch proof — EC2 instance created via Terraform against LocalStack.*


💻 Technical Setup
Environment: GitHub Codespaces

Simulation Engine: LocalStack (Docker)

Tooling: AWS CLI v2, Terraform v1.x

---

## 🔧 Starting LocalStack (Docker)

**Prerequisites:** Docker installed and running on your machine.

**Quick start (single container):**

```bash
# Run LocalStack with EC2 and IAM services enabled on the edge port 4566
docker run --rm -it -p 4566:4566 -p 4571:4571 \
  -e SERVICES=ec2,iam \
  -e DEFAULT_REGION=us-east-1 \
  localstack/localstack:latest
```

- For background mode, add `-d` (detached): `docker run -d ...`
- Adjust `SERVICES` to suit what you need (e.g., `s3,ec2,iam`).

**Using Docker Compose (recommended for repeatable setups):**

Create a `docker-compose.yml` in the project root:

```yaml
version: '3.8'
services:
  localstack:
    image: localstack/localstack:latest
    ports:
      - "4566:4566"
    environment:
      - SERVICES=ec2,iam
      - DEFAULT_REGION=us-east-1
      - DEBUG=1
    volumes:
      - ./localstack:/var/lib/localstack
```

Start with:

```bash
docker-compose up -d
```

**Verify LocalStack is running:**

```bash
# Check the container
docker ps

# Or run an AWS CLI command against the LocalStack endpoint
aws --endpoint-url=http://localhost:4566 ec2 describe-instances
```

**Troubleshooting tips:**
- If the port 4566 is already in use, stop the conflicting service or change the port mapping.
- If a service is missing, include it in `SERVICES` (e.g., `SERVICES=ec2,s3`).
- Use `DEBUG=1` for more verbose logs.

