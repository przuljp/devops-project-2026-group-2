## Phase 1: Planning the Design and Estimating the Cost

This section explains all configuration steps and structural choices made during Phase 1 so that the setup can be fully understood and reproduced.

### Task 1: Architectural Diagram & Network Topology Design

To prevent any single point of failure (SPOF) and guarantee high availability, the infrastructure is conceptually mapped across two distinct Availability Zones (`us-east-1a` and `us-east-1b`) within the `us-east-1` (N. Virginia) region.

#### 1. Network Subnet Segmentation and CIDR Allocations

The Virtual Private Cloud (VPC) uses a master address block of `10.0.0.0/16`. This layout is split into four separate, non-overlapping subnets to isolate public-facing entry points from secure internal backend tiers:

| Resource | CIDR Block | Purpose |
|----------|------------|---------|
| VPC Master Range | `10.0.0.0/16` | Main network boundary |
| Public Subnet A (us-east-1a) | `10.0.1.0/24` | External-facing components (ALB) |
| Public Subnet B (us-east-1b) | `10.0.2.0/24` | Multi-AZ public redundancy |
| Private Subnet A (us-east-1a) | `10.0.3.0/24` | Compute (EC2) + Database (RDS) |
| Private Subnet B (us-east-1b) | `10.0.4.0/24` | Compute (EC2) for horizontal scaling |

#### 2. Infrastructure Flow and Component Purposes

| Component | Placement | Purpose |
|-----------|-----------|---------|
| **Internet Gateway (IGW)** | VPC boundary | Enables public internet routing for components inside public subnets |
| **Application Load Balancer (ALB)** | Public subnets | Single point of contact; distributes incoming traffic to compute instances |
| **Auto Scaling Group (ASG)** | Both private subnets | Maintains baseline of 2 instances; ensures service availability across zones |
| **Amazon RDS for MySQL** | Private Subnet A | No direct internet path; only accessible internally by application tier |
| **AWS Secrets Manager** | Regional service | Stores database credentials securely; prevents hardcoding in application code |

> **Visual Reference:** The architectural diagram is available at `docs/architecture_phase1.png`

---

### Task 2: 12-Month Financial Cost Optimization Analysis

To achieve maximum cost efficiency, resource types were selected using a minimalist approach suitable for a Proof-of-Concept environment, keeping costs low while maintaining architecture standards. The cost estimate covers a 12-month lifecycle in the **US East (N. Virginia)** region.

#### Detailed Financial Breakdown by Service

##### 1. Amazon EC2 (Web App Compute Layer)

| Parameter | Value |
|-----------|-------|
| Configuration | 2 x t3.micro (2 vCPUs, 1 GiB RAM each) |
| Operating System | Linux / Ubuntu |
| Workload Type | Constant usage, 24/7 (730 hours/month per instance) |
| Storage | 10 GB gp3 per instance (3,000 IOPS, 125 MBps) |
| Backup | No snapshot storage |
| **Monthly Cost** | **$16.78** |

##### 2. Amazon RDS for MySQL (Data Layer)

| Parameter | Value |
|-----------|-------|
| Configuration | 1 x db.t3.micro (1 vCPU, 1 GiB RAM) |
| Deployment | Single-AZ (cost optimization) |
| Storage | 20 GB gp3 |
| **Monthly Cost** | **$54.86** |

##### 3. Elastic Load Balancing (Network Traffic Routing)

| Parameter | Value |
|-----------|-------|
| Configuration | 1 x Application Load Balancer (ALB) |
| Traffic Profile | Minimal baseline (0-1 GB monthly) |
| **Monthly Cost** | **$16.46** |

##### 4. AWS Secrets Manager (Security & Compliance)

| Parameter | Value |
|-----------|-------|
| Configuration | 1 active secret |
| Secret Duration | 30-day rotation |
| API Calls | 1,000 per month (baseline) |
| **Monthly Cost** | **$0.41** |

#### Financial Summary Metrics

| Metric | Amount |
|--------|--------|
| Upfront Costs | $0.00 |
| **Total Monthly Cost** | **$88.51** |
| **Total 12-Month Cost** | **$1,062.12** |

> **Reference:** Official cost estimate export is available at `docs/cost-estimate.pdf`

---

## Deployment & Reproduction Guide (Phase 1 Validation)

To reproduce the exact environment planning and validation artifacts established in Phase 1, follow these steps:

### Step 1: Replicate the Network Design

1. Open your diagramming tool (e.g., Lucidchart or Draw.io)
2. Create a master container representing the AWS Region (`us-east-1`)
3. Inside it, draw a VPC boundary labeled with CIDR block `10.0.0.0/16`
4. Divide the VPC into two vertical halves representing:
   - Availability Zone `us-east-1a`
   - Availability Zone `us-east-1b`
5. In each zone, create:
   - One Public Subnet (`10.0.1.0/24` and `10.0.2.0/24`)
   - One Private Subnet (`10.0.3.0/24` and `10.0.4.0/24`)
6. Place an **Internet Gateway** at the edge of the VPC
7. Place the **Application Load Balancer** across the public subnets
8. Set up an **Auto Scaling Group** spanning both private subnets with 2 EC2 instances
9. Add an **RDS MySQL database** into Private Subnet A
10. Map **AWS Secrets Manager** as an external regional service pointing to the ASG compute instances
11. Export the diagram as a PNG image and save to `docs/architecture_phase1.png`

### Step 2: Replicate the Cost Estimation

1. Navigate to the [AWS Pricing Calculator](https://calculator.aws)
2. Click **Create estimate** and set region to **US East (N. Virginia)**
3. **Add Amazon EC2:**
   - Quantity: 2
   - Usage: Constant (100% utilization)
   - Instance type: t3.micro
   - Storage: 10 GB gp3 (baseline IOPS 3000, throughput 125 MBps)
   - Snapshots: Disabled
4. **Add Amazon RDS for MySQL:**
   - Deployment: Single-AZ
   - Instance type: db.t3.micro
   - Storage: 20 GB gp3
5. **Add Elastic Load Balancing:**
   - Type: Application Load Balancer
   - Quantity: 1
   - LCU: 0 (baseline)
6. **Add AWS Secrets Manager:**
   - Number of secrets: 1
   - Duration: 30 days
   - API calls: 1,000 per month
7. Click **View Summary**, then **Export** → **PDF**
8. Save to `docs/cost-estimate.pdf`

## Next Steps

- **Phase 2:** Creating a basic functional web application (single EC2 instance deployment)
- **Phase 3:** Decoupling application components (RDS + Secrets Manager integration)
- **Phase 4:** Implementing high availability and scalability (ALB + ASG + load testing)

---

## References

- [AWS Architecture Icons](https://aws.amazon.com/architecture/icons/)
- [AWS Pricing Calculator](https://calculator.aws/)
- [AWS Well-Architected Framework](https://aws.amazon.com/architecture/well-architected/)