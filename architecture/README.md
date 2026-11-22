
# AWS Project 1 – Architecture Diagram

**Region:** eu-north-1 (Stockholm)

```mermaid
flowchart TB
    U[Users] --> I[Internet]
    I --> IGW[Internet Gateway]
    IGW --> ALB[Application Load Balancer]

    subgraph VPC["web-prod-vpc | 10.20.0.0/16"]
        subgraph AZ1["Availability Zone eu-north-1a"]
            PUB1["Public Subnet A<br/>10.20.1.0/24"]
            NAT["NAT Gateway"]
            APP1["Private App Subnet A<br/>10.20.3.0/24"]
            DB1["Private DB Subnet A<br/>10.20.5.0/24"]
        end

        subgraph AZ2["Availability Zone eu-north-1b"]
            PUB2["Public Subnet B<br/>10.20.2.0/24"]
            APP2["Private App Subnet B<br/>10.20.4.0/24"]
            DB2["Private DB Subnet B<br/>10.20.6.0/24"]
        end

        ALB --> APP1
        ALB --> APP2

        ASG["Auto Scaling Group<br/>Min 1 | Desired 1 | Max 3"]
        APP1 --> ASG
        APP2 --> ASG

        ASG --> EC2["EC2 Application Instances<br/>Nginx + Tomcat"]

        EC2 --> RDS["Amazon RDS PostgreSQL<br/>Single-AZ"]

        PUB1 --> NAT
        NAT -. Outbound internet access .-> APP1
        NAT -. Outbound internet access .-> APP2
    end

    EC2 --> CW["CloudWatch Metrics and Alarms"]
    CW --> SNS["SNS Notifications"]
```

## Architecture Notes

- The Application Load Balancer distributes incoming HTTP traffic to application instances.
- EC2 instances run in private application subnets.
- Auto Scaling manages application instance capacity.
- A single NAT Gateway provides outbound internet access.
- RDS PostgreSQL is private and configured as Single-AZ.
- CloudWatch and SNS provide monitoring and alert notifications.

**Note:** This is a lab implementation. The single NAT Gateway and Single-AZ database are not fully redundant configurations.
