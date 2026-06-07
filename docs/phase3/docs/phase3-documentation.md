# Phase 3 Documentation – Decoupling Application Components

## Overview

The objective of Phase 3 was to separate the database layer from the web application layer. In Phase 2, both the Node.js application and MySQL database were hosted on the same EC2 instance. During this phase, the database was migrated to Amazon RDS while the web application was deployed on a dedicated EC2 instance.

The completed architecture improves scalability, security, and follows AWS cloud best practices by separating application and database resources.

---

# Task 1 – Changing the VPC Configuration

## Objective

Update the network infrastructure to support hosting the database separately from the web application.

## Implementation

The existing VPC from Phase 2 was reused and extended with private subnets to support Amazon RDS deployment.

### VPC Configuration

| Resource   | Value       |
| ---------- | ----------- |
| VPC Name   | project-vpc |
| CIDR Block | 10.0.0.0/16 |

### Private Subnets

| Resource          | Value       |
| ----------------- | ----------- |
| Private Subnet 1  | 10.0.3.0/24 |
| Availability Zone | us-east-1a  |
| Private Subnet 2  | 10.0.4.0/24 |
| Availability Zone | us-east-1b  |

### Result

The VPC was successfully updated to support a separate database layer hosted inside private subnets.

---

# Task 2 – Creating and Configuring Amazon RDS

## Objective

Deploy a managed MySQL database using Amazon RDS.

### RDS Configuration

| Resource            | Value        |
| ------------------- | ------------ |
| Database Identifier | database-1   |
| Engine              | MySQL        |
| Instance Class      | db.t3.micro |
| Public Access       | Disabled     |

### Security Configuration

The database was configured to accept connections only from approved security groups.

Inbound access was allowed from:

* Phase 3 Web Server Security Group
* AWS Cloud9 Security Group

### Result

Amazon RDS was successfully deployed and configured inside the project VPC.

---

# Task 3 – Configuring the Development Environment

## Objective

Provision an AWS Cloud9 environment for administration and migration tasks.

### Cloud9 Configuration

| Resource         | Value          |
| ---------------- | -------------- |
| Environment Name | project-cloud9 |
| Instance Type    | t3.micro       |
| Connection Type  | SSH            |

### Result

The Cloud9 environment was successfully deployed and used to perform AWS CLI operations and database migration tasks.

---

# Task 4 – Provisioning Secrets Manager

## Objective

Store database credentials securely and allow the application to retrieve them dynamically.

## Implementation

A secret named **Mydbsecret** was created in AWS Secrets Manager.

The secret stores:

* Database username
* Database password
* Database endpoint
* Database name

The application retrieves these values dynamically during runtime.

### Result

Database credentials were successfully stored in AWS Secrets Manager and integrated with the web application.

---

# Task 5 – Provisioning a New Instance for the Web Server

## Objective

Deploy the web application on a dedicated EC2 instance.

### EC2 Configuration

| Resource          | Value             |
| ----------------- | ----------------- |
| Instance Name     | phase3-web-server |
| Instance Type     | t3.micro          |
| Operating System  | Ubuntu 26.04 LTS  |
| Region            | us-east-1         |
| Availability Zone | us-east-1a        |

### IAM Configuration

The following IAM profile was attached to the instance:

```text
LabInstanceProfile
```

This allowed the application to securely access Secrets Manager.

### Security Group Configuration

#### Inbound Rules

| Protocol | Port | Source    |
| -------- | ---- | --------- |
| SSH      | 22   | Anywhere  |
| HTTP     | 80   | 0.0.0.0/0 |

### Application Deployment

The provided application deployment script was used during instance creation.

The application was automatically installed and started using EC2 User Data.

### Application URL

```text
http://54.204.231.197
```

### Result

The application was successfully deployed on a dedicated EC2 instance and was accessible through the public internet.

---

# Task 6 – Migrating the Database

## Objective

Migrate the existing STUDENTS database from the Phase 2 EC2 instance to Amazon RDS.

## Implementation

Database migration was performed using AWS Cloud9.

The existing database was exported from the original EC2 instance using:

```bash
mysqldump -h 10.0.1.154 -u nodeapp -p --databases STUDENTS > data.sql
```

The exported database was imported into Amazon RDS using:

```bash
mysql -h database-1.cxsv4y3kmjqs.us-east-1.rds.amazonaws.com -u admin -p < data.sql
```

### Verification

The migration was verified by connecting to Amazon RDS and confirming the existence of:

```text
Database: STUDENTS
Table: students
```

### Result

The STUDENTS database was successfully migrated from the Phase 2 EC2 instance to Amazon RDS.

---

# Task 7 – Testing the Application

## Objective

Verify that the web application can successfully communicate with Amazon RDS.

### Functional Testing

The following operations were successfully tested:

* Viewing student records
* Adding student records
* Retrieving records from Amazon RDS

A test student record was successfully inserted through the application interface.

### Result

The web application successfully communicated with Amazon RDS and all required functionality operated correctly.

---

# Screenshots

The following screenshots were captured as evidence of successful deployment:

* Private Subnets
* Amazon RDS Instance
* RDS Connectivity Configuration
* Cloud9 Environment
* Secrets Manager Secret
* Phase 3 EC2 Instance Running
* Application Home Page
* Database Migration
* Student Records List
* Add Student Operation

Screenshots are available in:

```text
docs/phase3/images
```

---

# Conclusion

Phase 3 was successfully completed. The database layer was migrated from a local MySQL installation on an EC2 instance to Amazon RDS. AWS Secrets Manager was used to securely manage database credentials, and the application was deployed on a dedicated EC2 instance.

The final architecture separates the application and database layers, improving scalability, security, and maintainability while maintaining full application functionality.
