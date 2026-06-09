# Phase 2 Documentation – Deploying a Basic Web Application on AWS

## Overview

The objective of Phase 2 was to deploy a functional web application on AWS using a single virtual machine. The application and database were hosted on the same Amazon EC2 instance inside a custom Virtual Private Cloud (VPC). The deployed solution serves as a proof of concept (POC) that demonstrates successful hosting of a web application on AWS infrastructure.

---

# Task 1 – Creating a Virtual Network

## Objective

Create a virtual network environment to host the web application.

## Implementation

A custom Amazon VPC was created to isolate the project resources from the default AWS network.

### VPC Configuration

| Resource   | Value       |
| ---------- | ----------- |
| VPC Name   | project-vpc |
| CIDR Block | 10.0.0.0/16 |

### Public Subnet

| Resource          | Value                             |
| ----------------- | --------------------------------- |
| Name              | project-subnet-public1-us-east-1a |
| CIDR Block        | 10.0.1.0/24                       |
| Availability Zone | us-east-1a                        |

### Internet Connectivity

The subnet was associated with a route table containing a route to an Internet Gateway, allowing resources within the subnet to communicate with the Internet.

### Result

The VPC and public subnet were successfully configured and provided network connectivity for the EC2 instance.

![Example 1](https://github.com/przuljp/devops-project-2026-group-2/blob/development/docs/phase2/images/VPC.png?raw=true)

---

# Task 2 – Creating a Virtual Machine

## Objective

Deploy a web application and database on a single EC2 instance.

### EC2 Configuration

| Resource          | Value             |
| ----------------- | ----------------- |
| Instance Name     | phase2-web-server |
| Instance Type     | t3.micro          |
| Operating System  | Ubuntu 26.04 LTS  |
| Region            | us-east-1         |
| Availability Zone | us-east-1a        |

### Security Group Configuration

A custom security group named **phase2-web-sg** was created.

#### Inbound Rules

| Protocol   | Port | Source    |
| ---------- | ---- | --------- |
| SSH        | 22   | My IP     |
| HTTP       | 80   | 0.0.0.0/0 |
| Custom TCP | 3000 | 0.0.0.0/0 |

#### Outbound Rules

| Protocol    | Destination |
| ----------- | ----------- |
| All Traffic | 0.0.0.0/0   |

### Software Installation

The following software packages were installed on the EC2 instance:

* Node.js
* npm
* MySQL Server

### Application Deployment

The provided SolutionCodePOC application was extracted and deployed on the EC2 instance.

Application directory:

```bash
/home/ubuntu/resources/codebase_partner
```

Dependencies were installed using:

```bash
npm install
```

The application was started using:

```bash
node index.js
```

![Example 2](https://github.com/przuljp/devops-project-2026-group-2/blob/development/docs/phase2/images/EC2_1.png?raw=true)
![Example 3](https://github.com/przuljp/devops-project-2026-group-2/blob/development/docs/phase2/images/EC2_2.png?raw=true)

---

# Database Configuration

## MySQL Setup

A local MySQL database was configured on the EC2 instance.

### Database

```sql
CREATE DATABASE STUDENTS;
```

### User

```sql
CREATE USER 'nodeapp'@'%' IDENTIFIED BY 'student12';
GRANT ALL PRIVILEGES ON *.* TO 'nodeapp'@'%';
FLUSH PRIVILEGES;
```

### Students Table

```sql
CREATE TABLE students(
    id INT NOT NULL AUTO_INCREMENT,
    name VARCHAR(255) NOT NULL,
    address VARCHAR(255) NOT NULL,
    city VARCHAR(255) NOT NULL,
    state VARCHAR(255) NOT NULL,
    email VARCHAR(255) NOT NULL,
    phone VARCHAR(100) NOT NULL,
    PRIMARY KEY(id)
);
```

### MySQL Network Configuration

The MySQL server configuration was modified to allow network connections:

```text
bind-address = 0.0.0.0
```

The MySQL service was restarted successfully after the modification.

---

# Application Configuration

The application configuration was updated to use the local database.

### Database Parameters

| Setting  | Value     |
| -------- | --------- |
| Host     | localhost |
| Database | STUDENTS  |
| Username | nodeapp   |
| Password | student12 |

The application successfully established a connection to MySQL.

![Example 4](https://github.com/przuljp/devops-project-2026-group-2/blob/development/docs/phase2/images/MySQL_1.png?raw=true)

---

# Task 3 – Testing the Deployment

## Objective

Verify that the application is accessible from the Internet and fully functional.

### Application URL at the Time of Testing

```text
http://54.175.185.197:3000
```

### Functional Testing

The following operations were successfully tested:

* Viewing student records
* Adding student records
* Updating student records
* Deleting student records

### Result

The web application successfully communicated with the MySQL database and all CRUD operations worked correctly.

The following output proves the functionality of basic CRUD operations on the website.
Output was given by the Amazon console:

```text
Successfully connected to the database.
created supplier:  {
  id: undefined,
  name: 'Amira Peric',
  address: 'Perkovic 24',
  city: 'Sarajevo',
  state: 'Bosnia and Herzegovina',
  email: 'amira.peric@gmail.com',
  phone: '38761626626'
}
Successfully connected to the database.
Students:  [
  TextRow {
    id: 3,
    name: 'Amira Peric',
    address: 'Perkovic 24',
    city: 'Sarajevo',
    state: 'Bosnia and Herzegovina',
    email: 'amira.peric@gmail.com',
    phone: '38761626626'
  }
]
Successfully connected to the database.
found supplier:  TextRow {
  id: 3,
  name: 'Amira Peric',
  address: 'Perkovic 24',
  city: 'Sarajevo',
  state: 'Bosnia and Herzegovina',
  email: 'amira.peric@gmail.com',
  phone: '38761626626'
}
Successfully connected to the database.
updated supplier:  {
  id: '3',
  name: 'Amira Peric',
  address: 'Perkovic 25',
  city: 'Sarajevo',
  state: 'Bosnia and Herzegovina',
  email: 'amira.peric@gmail.com',
  phone: '38761626626'
}
Successfully connected to the database.
Students:  [
  TextRow {
    id: 3,
    name: 'Amira Peric',
    address: 'Perkovic 25',
    city: 'Sarajevo',
    state: 'Bosnia and Herzegovina',
    email: 'amira.peric@gmail.com',
    phone: '38761626626'
  }
]
Successfully connected to the database.
found supplier:  TextRow {
  id: 3,
  name: 'Amira Peric',
  address: 'Perkovic 25',
  city: 'Sarajevo',
  state: 'Bosnia and Herzegovina',
  email: 'amira.peric@gmail.com',
  phone: '38761626626'
}
Successfully connected to the database.
deleted student with id:  3
Successfully connected to the database.
Students:  []
```

![Example 5](https://github.com/przuljp/devops-project-2026-group-2/blob/development/docs/phase2/images/website.png?raw=true)
![Example 6](https://github.com/przuljp/devops-project-2026-group-2/blob/development/docs/phase2/images/website_2.png?raw=true)
![Example 7](https://github.com/przuljp/devops-project-2026-group-2/blob/development/docs/phase2/images/website_3.png?raw=true)
![Example 8](https://github.com/przuljp/devops-project-2026-group-2/blob/development/docs/phase2/images/website_4.png?raw=true)
![Example 9](https://github.com/przuljp/devops-project-2026-group-2/blob/development/docs/phase2/images/website_5.png?raw=true)

---

# Conclusion

Phase 2 was successfully completed. A fully functional student management application was deployed on AWS using a custom VPC and an EC2 instance. The Node.js application and MySQL database were configured and tested successfully. The application was publicly accessible through the EC2 public IP address and supported all required CRUD operations.

This deployment serves as the foundation for Phase 3, where the database layer will be separated from the application layer using Amazon RDS and AWS Secrets Manager.

![Example 10](https://github.com/przuljp/devops-project-2026-group-2/blob/development/docs/phase2/images/architecture-phase2.png?raw=true)
