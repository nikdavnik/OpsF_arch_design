Cloud Infrastructure Architecture for Innovate Inc.
1. Cloud Environment Structure

We recommend using AWS based on:

    Stronger Kubernetes support (EKS).

    Mature managed services (RDS, IAM, networking).

    Cost optimizations (Graviton, Spot instances, Karpenter).

    Widespread expertise availability.

AWS Account Structure

    1. Management Account: Central billing, security tooling.

    2. Workload Account: Hosts production EKS, databases, services.

    3. Dev/Test Account: Separate environment for staging/testing.

    Reason:

    Clear billing separation.

    Blast radius reduction (staging mistakes won't impact production).

    Follows AWS best practices (multi-account organization via AWS Organizations).

2. Network Design
VPC Architecture

    One VPC per environment (prod/dev).

    Subnets:

        Public Subnets: For load balancers (ALB).

        Private Subnets: For application workloads (EKS nodes).

        Isolated Subnets: For database (RDS PostgreSQL), no internet access.

Key Network Components

    NAT Gateway: Allow EKS nodes to access the internet securely.

    VPC Endpoints: Private access to AWS services (S3, DynamoDB, etc.).

    Security Groups: Fine-grained access control between layers.

    Network ACLs: Additional subnet-level security if needed.

Network Security

    Only load balancer exposed publicly.

    Private nodes (EKS) — no public IPs.

    RDS DB accessible only from inside VPC.

3. Compute Platform (Kubernetes)
Kubernetes: Amazon EKS

    Managed Kubernetes — AWS handles control plane HA.

    Karpenter for autoscaling nodes dynamically based on pod requirements.

    Multi-architecture Node Pools:

        Spot Instances for cost savings (t4g.medium, m6g.large Graviton).

        On-demand fallback for critical workloads.

Node Group Strategy

    System Node Group (baseline, on-demand, small):
    For system components (CoreDNS, Metrics Server, etc.).

    Workload Node Pools (Karpenter-managed):
    Dynamic scaling based on application load.

Scaling Strategy

    Cluster Autoscaler/Karpenter.

    Horizontal Pod Autoscaler (HPA) on apps.

    CPU and memory resource requests/limits per pod.

Containerization Strategy

    Docker images for frontend/backend.

    Use multi-stage builds to keep images small.

    Push to Amazon ECR (Elastic Container Registry).

Deployment Strategy

    CI/CD Pipeline (GitHub Actions, GitLab CI, or AWS CodePipeline):

        Build → Test → Push Docker image → Deploy to EKS.

        Use GitOps (e.g., ArgoCD) for Kubernetes deployments.

4. Database (PostgreSQL)
Recommendation: Amazon RDS for PostgreSQL

    Fully managed PostgreSQL.

    Automated backups, patching, minor upgrades.

    Multi-AZ deployment for high availability.

    Encryption at rest and in transit.

Database Strategy

    Daily Backups: Automated + manual snapshots before major deployments.

    Point-in-Time Recovery: Enabled for disaster recovery.

    Replication: Multi-AZ enabled by default.

    Read Replicas: Added later for scaling reads (if necessary).

    Security:

        Encrypted connections (SSL/TLS).

        VPC-only access.

        Secrets stored in AWS Secrets Manager.

        Strict IAM and Security Group controls.
