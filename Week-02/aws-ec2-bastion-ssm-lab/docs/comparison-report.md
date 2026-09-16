
# Architectural Comparison Report: Bastion Host vs. AWS SSM Session Manager

## Executive Summary
In Amazon Web Services (AWS) environments, securing administrative shell access to Amazon EC2 instances running within private subnets is a core operational requirement. This document evaluates two primary connectivity strategies: the traditional **Bastion Host (SSH Jump Box)** approach and modern **AWS Systems Manager (SSM) Session Manager**.

---

## 📊 Technical Comparison Matrix

| Evaluation Criteria | Bastion Host (SSH Jump Box) | AWS SSM Session Manager |
| :--- | :--- | :--- |
| **Inbound Security Profile** | Requires open Port 22 inbound from administrator IP addresses. | **Zero inbound open ports required** (Port 22 stays completely closed). |
| **Network Infrastructure** | Requires a dedicated EC2 instance in a public subnet with a Public IP. | Works natively in private subnets via NAT Gateway or VPC Endpoints. |
| **Authentication & Access** | Managed via local Linux users, SSH key pairs (`.pem`), and SSH agent forwarding (`-A`). | Integrated natively with **AWS IAM** policies and SSO/MFA. |
| **Audit & Governance** | Minimal native logging; requires complex syslog/bastion audit setup. | Automatic, centralized logging of full terminal sessions to **S3** or **CloudWatch**. |
| **Maintenance Overhead** | High (ongoing server patching, IP maintenance, SSH key rotation). | **Zero server maintenance** (Fully managed native AWS service). |

---

## 🏗️ Detailed Architecture Breakdown

### 1. Bastion Host Architecture (SSH Tunneling)
* **Design Pattern:** A public-facing EC2 instance (`public-web-server`) acts as an SSH jump server in a public subnet. Administrators connect via `ssh -A ec2-user@<bastion-ip>` and pivot to the private database instance (`10.0.2.10`).
* **Security Trade-offs:**
  * **Exposed Surface Area:** Leaving Port 22 open to public or semi-public networks invites port scanning and brute-force SSH attacks.
  * **Credential Management:** Managing private SSH keys across team members poses security risks and operational complexity.

### 2. AWS SSM Session Manager Architecture
* **Design Pattern:** The `amazon-ssm-agent` running on the private instance initiates outbound TLS-encrypted connections (Port 443) to Systems Manager endpoints through a NAT Gateway or VPC Endpoint.
* **Security Benefits:**
  * **No Inbound Exposure:** Security groups do not require any open inbound ports.
  * **IAM-Driven Access:** Authentication relies entirely on IAM policies and Instance Profiles (`AmazonSSMManagedInstanceCore`).

---

## 💡 Production Recommendation

**AWS Systems Manager Session Manager is the recommended standard for enterprise cloud production environments.**

By completely eliminating public IP requirements, closing all inbound ports, and delivering immutable, centralized session logging to CloudWatch/S3, SSM Session Manager significantly reduces the VPC attack surface while easing compliance auditing. Bastion hosts should be phased out in favor of SSM-managed node connectivity.
