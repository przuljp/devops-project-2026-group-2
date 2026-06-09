# Phase 1 Documentation – Architecture Planning and Cost Estimation

## Overview

The objective of Phase 1 was to design a cloud architecture capable of hosting the Student Records web application while satisfying the project requirements.

The architecture was planned according to AWS Well-Architected Framework principles with emphasis on:

* High availability
* Scalability
* Security
* Cost optimization
* Load balancing
* Performance

Before deploying infrastructure, an architecture design was created and a cost estimate was developed using AWS Pricing Calculator.

The planned environment is intended to support thousands of users while remaining reliable and operational during peak admissions periods.

---

# Task 1 – Creating an Architectural Diagram

## Objective

Design an AWS architecture capable of hosting the Student Records application while satisfying all functional and non-functional requirements.

## Implementation

### Architecture Components

| Component                 | Purpose                                         |
| ------------------------- | ----------------------------------------------- |
| VPC                       | Isolated virtual network for hosting resources  |
| Public Subnets            | Host internet-facing components                 |
| Private Subnets           | Host internal application and database services |
| Application Load Balancer | Distributes incoming traffic                    |
| Auto Scaling Group        | Automatically scales application servers        |
| Amazon EC2                | Hosts theapplication                            |
| Amazon RDS (MySQL)        | Stores student records                          |
| AWS Secrets Manager       | Stores database credentials                     |

### Network Layout

| Resource         | CIDR        |
| ---------------- | ----------- |
| VPC              | 10.0.0.0/16 |
| Public Subnet A  | 10.0.1.0/24 |
| Public Subnet B  | 10.0.2.0/24 |
| Private Subnet A | 10.0.3.0/24 |
| Private Subnet B | 10.0.4.0/24 |

### Security Design

| Resource                  | Access                     |
| ------------------------- | -------------------------- |
| Application Load Balancer | HTTP (80) from Internet    |
| EC2 Instances             | HTTP (80) from ALB         |
| Amazon RDS                | MySQL (3306) from EC2 only |

### Result

The proposed architecture provides:

* Separation of application and database layers
* Support for horizontal scaling
* High availability across Availability Zones
* Controlled database access
* Load balancing for traffic distribution

---

# Task 2 – Developing a Cost Estimate

## Objective

Estimate the cost of running the proposed solution in the us-east-1 Region for 12 months.

## Implementation

AWS Pricing Calculator was used to estimate infrastructure expenses.

The estimate was prepared using the planned production architecture.

Services included in the estimate:

* Amazon EC2
* Amazon RDS for MySQL
* Elastic Load Balancing
* AWS Secrets Manager

### Estimated Infrastructure Cost

| Metric                 |      Cost |
| ---------------------- | --------: |
| Upfront Cost           |     $0.00 |
| Monthly Cost           |    $88.51 |
| Total Cost (12 Months) | $1,062.12 |

### Service Breakdown

| Service                | Monthly Cost |
| ---------------------- | -----------: |
| Amazon EC2             |       $16.78 |
| Amazon RDS for MySQL   |       $54.86 |
| Elastic Load Balancing |       $16.46 |
| AWS Secrets Manager    |        $0.41 |

### Estimated Resource Configuration

#### Amazon EC2

| Setting             | Value     |
| ------------------- | --------- |
| Instance Type       | t3.micro  |
| Number of Instances | 2         |
| OS                  | Linux     |
| Pricing             | On-Demand |
| EBS Storage         | 10 GB     |

#### Amazon RDS

| Setting       | Value       |
| ------------- | ----------- |
| Engine        | MySQL       |
| Instance Type | db.t3.micro |
| Storage       | 20 GB gp3   |
| Deployment    | Single-AZ   |

#### Elastic Load Balancer

| Setting  | Value                     |
| -------- | ------------------------- |
| Type     | Application Load Balancer |
| Quantity | 1                         |

#### AWS Secrets Manager

| Setting           | Value      |
| ----------------- | ---------- |
| Number of Secrets | 1          |
| API Calls         | 1000/month |

### Cost Analysis

Amazon RDS represents the largest portion of infrastructure expenses due to managed database hosting.

Compute costs remain low because small EC2 instances were selected for the proof-of-concept deployment.

Secrets Manager contributes minimal additional cost while improving credential security.

### Result

The estimated annual infrastructure cost remained within acceptable limits for a proof-of-concept environment while still providing scalability and availability capabilities.

You can find the full PDF document under the `docs/phase1/docs`

---

# Planned Architecture Components

## Application Load Balancer

Receives incoming user traffic and distributes requests across application servers.

## Auto Scaling Group

Provides scalability by automatically adjusting the number of EC2 instances.

## Amazon EC2

Runs the Student Records web application.

## Amazon RDS

Stores student data and application records.

## AWS Secrets Manager

Stores sensitive configuration values and database credentials.

---

# Final Planned Architecture

The planned infrastructure contains:

* One VPC (10.0.0.0/16)
* Two public subnets
* Two private subnets
* Application Load Balancer
* Auto Scaling Group
* Multiple EC2 instances
* Amazon RDS MySQL
* AWS Secrets Manager

Users access the application through the Application Load Balancer, which distributes traffic across EC2 instances.

Application servers retrieve credentials securely and communicate with Amazon RDS for data storage.

![Example 1](https://github.com/przuljp/devops-project-2026-group-2/blob/development/docs/phase1/images/architecture-phase1.png?raw=true)

---

# Conclusion

Phase 1 established the foundation for the remaining implementation phases.

The architecture was designed to meet availability, security, performance, and scalability requirements while remaining cost conscious.

The cost estimate demonstrated that the planned infrastructure could be operated for approximately $1,062 annually in the selected AWS Region.

This planning phase provided the blueprint for deployment and implementation in later phases.
