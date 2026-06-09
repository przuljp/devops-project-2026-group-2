# Phase 4 Documentation – High Availability and Auto Scaling

## Overview

The objective of Phase 4 was to improve the availability, scalability, and fault tolerance of the application deployed in previous phases.

In Phase 3, the web application was hosted on a single EC2 instance connected to an Amazon RDS database. While functional, the architecture represented a single point of failure because the application depended on one web server.

During Phase 4, the architecture was enhanced by introducing:

* Application Load Balancer (ALB)
* Auto Scaling Group (ASG)
* Multiple Availability Zones
* Multiple EC2 application instances
* Load testing to validate scaling capabilities

The final architecture provides high availability and improved resiliency by distributing traffic across multiple EC2 instances located in different Availability Zones.

---

# Task 1 – Creating an Application Load Balancer

## Objective

Create an Application Load Balancer to distribute incoming traffic across multiple EC2 instances.

## Implementation

An internet-facing Application Load Balancer was created inside the existing VPC.

### Configuration

| Resource           | Value                     |
| ------------------ | ------------------------- |
| Type               | Application Load Balancer |
| Scheme             | Internet-facing           |
| Protocol           | HTTP                      |
| Port               | 80                        |
| Availability Zones | us-east-1a, us-east-1b    |

The load balancer was configured to route requests to a target group containing the application instances.

![Example 7](https://github.com/przuljp/devops-project-2026-group-2/blob/development/docs/phase4/images/target-groups.png?raw=true)


### Result

The application became accessible through the load balancer DNS endpoint instead of directly through an EC2 public IP address.

![Example 1](https://github.com/przuljp/devops-project-2026-group-2/blob/development/docs/phase4/images/load_balancer.png?raw=true)

![Example 2](https://github.com/przuljp/devops-project-2026-group-2/blob/development/docs/phase4/images/load_balancer_2.png?raw=true)

---

# Task 2 – Creating an Auto Scaling Group

## Objective

Automatically manage the number of EC2 instances based on demand.

## Implementation

A launch template was created using the Phase 3 application server configuration.

The Auto Scaling Group was configured to launch instances across multiple Availability Zones.

### Auto Scaling Configuration

| Setting            | Value                  |
| ------------------ | ---------------------- |
| Minimum Capacity   | 1                      |
| Desired Capacity   | 1                      |
| Maximum Capacity   | 2                      |
| Availability Zones | us-east-1a, us-east-1b |

The Auto Scaling Group was attached to the Application Load Balancer target group.

### Result

Instances launched by Auto Scaling are automatically registered with the load balancer and begin serving traffic after passing health checks.

![Example 8](https://github.com/przuljp/devops-project-2026-group-2/blob/development/docs/phase4/images/launch_template.png?raw=true)

![Example 3](https://github.com/przuljp/devops-project-2026-group-2/blob/development/docs/phase4/images/auto_scaling_groups.png?raw=true)

![Example 4](https://github.com/przuljp/devops-project-2026-group-2/blob/development/docs/phase4/images/auto_scaling_groups_2.png?raw=true)

---

# Task 3 – Accessing and Testing the Application

## Objective

Verify that the application operates correctly behind the load balancer.

## Implementation

The application was accessed through the Application Load Balancer DNS name.

Several operations were performed to validate functionality:

* Viewing student records
* Adding a new student
* Updating student information
* Retrieving records from the Amazon RDS database

### Result

The application successfully communicated with the RDS database through the load balancer endpoint.

All CRUD operations functioned correctly after configuration adjustments.

![Example 5](https://github.com/przuljp/devops-project-2026-group-2/blob/development/docs/phase4/images/website_1.png?raw=true)

![Example 6](https://github.com/przuljp/devops-project-2026-group-2/blob/development/docs/phase4/images/website_2.png?raw=true)

![Example 7](https://github.com/przuljp/devops-project-2026-group-2/blob/development/docs/phase4/images/website_3.png?raw=true)

---

# Task 4 – Load Testing the Application

## Objective

Generate traffic against the application and observe the behavior of the Auto Scaling Group.

## Implementation

The AWS Cloud9 environment was used to install the load testing utility.

### Installation

```bash
sudo npm install -g loadtest
```

### Load Test Command

```bash
loadtest --rps 1000 -c 500 -k http://<ALB-DNS-NAME>
```

### Load Test Results

| Metric              | Value  |
| ------------------- | ------ |
| Requests per Second | 1000   |
| Concurrent Clients  | 500    |
| Completed Requests  | 9997   |
| Total Errors        | 0      |
| Mean Latency        | 3.1 ms |
| Effective RPS       | 1000   |

### Performance Summary

The application handled approximately 10,000 requests within a 10-second testing period.

No errors were reported during testing, demonstrating that the load balancer and application infrastructure remained operational under heavy load.

![Example 6](https://github.com/przuljp/devops-project-2026-group-2/blob/development/docs/phase4/images/cloud9.png?raw=true)

---

# Architecture Components

## Application Load Balancer

Responsible for distributing incoming HTTP requests across available EC2 instances.

## Auto Scaling Group

Provides automated instance management and high availability by maintaining the desired number of application servers.

## Amazon EC2

Hosts the Node.js web application instances.

## Amazon RDS

Stores student application data using a managed MySQL database service.

## AWS Secrets Manager

Securely stores database credentials and connection information used by the application.

---

# Final Architecture

The final architecture contains:

* One VPC (10.0.0.0/16)
* Two public subnets

  * 10.0.1.0/24
  * 10.0.2.0/24
* Two private subnets

  * 10.0.3.0/24
  * 10.0.4.0/24
* Application Load Balancer
* Auto Scaling Group
* Multiple EC2 instances
* Amazon RDS MySQL
* AWS Secrets Manager

Traffic flows from users through the Application Load Balancer to EC2 instances running in multiple Availability Zones. The application instances retrieve database credentials from AWS Secrets Manager and communicate with Amazon RDS for data storage.

---

# Conclusion

Phase 4 completed the transition from a single-instance deployment to a highly available architecture.

The introduction of an Application Load Balancer and Auto Scaling Group improved fault tolerance and prepared the application for handling increased traffic loads.

The final environment follows AWS best practices by separating application and database layers, distributing workloads across Availability Zones, and supporting automated scaling.

![Example 9](https://github.com/przuljp/devops-project-2026-group-2/blob/development/docs/phase4/images/architecture-phase4.png?raw=true)