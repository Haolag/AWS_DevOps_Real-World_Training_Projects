# AWS_DevOps_Real-World_Training_Projects
readme_content = """# AWS DevOps Task 1: Building a Production-Grade VPC & Network Infrastructure

[![AWS](https://img.shields.io/badge/AWS-VPC%20%26%20Networking-orange?logo=amazon-aws)](https://aws.amazon.com/)
[![Terraform](https://img.shields.io/badge/IaC-Terraform%20v1.5+-purple?logo=terraform)](https://www.terraform.io/)
[![License](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

A complete, production-grade AWS Virtual Private Cloud (VPC) network architecture designed for high availability, fault tolerance, and defense-in-depth security across multiple Availability Zones (AZs). Built with strict separation between public, private application, and isolated database tiers.

---

## 📋 Executive Summary & Problem Statement

Modern enterprise web applications require a highly secure, scalable, and multi-AZ network foundation that strictly segregates public-facing components (e.g., Application Load Balancers, Bastions) from internal application backends and database layers.

### Key Objectives
* Design a `/16` VPC spanning **two Availability Zones** (`us-east-1a` and `us-east-1b`).
* Implement a 3-tier architecture (Public Subnets, Private App Subnets, Private Database Subnets).
* Establish safe outbound internet connectivity for private application nodes using Elastic IP-backed **NAT Gateways** without exposing them to inbound internet access.
* Enforce **Least Privilege Access** using stateful Security Groups and stateless Network Access Control Lists (NACLs).
* Provide full automation via **Terraform Infrastructure as Code (IaC)**, operational troubleshooting logs, and cost breakdown.

---

## 🏗 Architecture & Network Topology

### Logical Network Diagram

```text
                                  +-------------------------------------------------------+
                                  |                     INTERNET                          |
                                  +-------------------------------------------------------+
                                                              ^
                                                              | Inbound / Outbound
                                                              v
                                  +-------------------------------------------------------+
                                  |                Internet Gateway (IGW)                 |
                                  +-------------------------------------------------------+
                                                              |
                                                              v
+-----------------------------------------------------------------------------------------------------------------------------------+
| AWS VPC: 10.0.0.0/16                                                                                                              |
|                                                                                                                                   |
|  +-------------------------------------------------------------+  +-------------------------------------------------------------+  |
|  | AVAILABILITY ZONE 1: us-east-1a                            |  | AVAILABILITY ZONE 2: us-east-1b                            |  |
|  |                                                             |  |                                                             |  |
|  |  [ Public Subnet 1a ] 10.0.1.0/24                          |  |  [ Public Subnet 1b ] 10.0.2.0/24                          |  |
|  |  +-------------------------------------------------------+  |  |  +-------------------------------------------------------+  |  |
|  |  | - Public ALB / Bastion Host                           |  |  |  | - Public ALB / Standby Bastion                       |  |  |
|  |  | - NAT Gateway 1a (EIP) ------+                        |  |  |  | - NAT Gateway 1b (EIP)                               |  |  |
|  |  +------------------------------|------------------------+  |  |  +-------------------------------------------------------+  |  |
|  |                                 |                        |  |                                                             |  |
|  |                                 v Outbound Route         |  |                                                             |  |
|  |  [ Private App Subnet 1a ] 10.0.11.0/24                     |  |  [ Private App Subnet 1b ] 10.0.12.0/24                     |  |
|  |  +-------------------------------------------------------+  |  |  +-------------------------------------------------------+  |  |
|  |  | - Web / App Tier (EC2 / ECS / EKS)                    |  |  |  | - Web / App Tier (EC2 / ECS / EKS)                    |  |  |
|  |  | - Outbound Internet via NAT GW                        |  |  |  | - Outbound Internet via NAT GW                        |  |  |
|  |  +-------------------------------------------------------+  |  |  +-------------------------------------------------------+  |  |
|  |                                                             |  |                                                             |  |
|  |  [ Private DB Subnet 1a ] 10.0.21.0/24                      |  |  [ Private DB Subnet 1b ] 10.0.22.0/24                      |  |
|  |  +-------------------------------------------------------+  |  |  +-------------------------------------------------------+  |  |
|  |  | - Aurora / RDS PostgreSQL Primary                     |  |  |  | - Aurora / RDS PostgreSQL Standby                     |  |  |
|  |  | - Fully Isolated (No Internet Route)                  |  |  |  | - Fully Isolated (No Internet Route)                  |  |  |
|  |  +-------------------------------------------------------+  |  |  +-------------------------------------------------------+  |  |
|  +-------------------------------------------------------------+  +-------------------------------------------------------------+  |
+-----------------------------------------------------------------------------------------------------------------------------------+
