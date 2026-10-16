## 1. What is Cloud Computing?

Cloud computing is the delivery of computing services—such as servers, storage, databases, networking, and software—over the internet on demand.

Key benefits:

- Pay as you go: Pay for the resources you use.
- Scalability: Increase or decrease resources as needed.
- High availability: Design applications to remain accessible.
- Less maintenance: Cloud providers manage much of the underlying infrastructure.

Types of cloud:

- Public cloud: Infrastructure provided over the internet by a cloud provider.
- Private cloud: Infrastructure dedicated to an organization.
- Hybrid cloud: Combination of on-premises infrastructure and cloud.
- Multicloud: Using multiple cloud providers.

Example: Instead of purchasing a physical server, create a virtual machine on Google Cloud and pay for its usage.

## 2. GCP Overview and Key Services

Google Cloud Platform (GCP), now commonly called Google Cloud, provides cloud services for hosting applications, storing data, networking, analytics, and automation.

| Category       | GCP service                          | Purpose                                 |
| -------------- | ------------------------------------ | --------------------------------------- |
| Compute        | Compute Engine                       | Virtual machines                        |
| Containers     | Google Kubernetes Engine (GKE)       | Managed Kubernetes                      |
| Serverless     | Cloud Run                            | Run containers without managing servers |
| Storage        | Cloud Storage                        | Store files and objects                 |
| Database       | Cloud SQL                            | Managed relational databases            |
| Networking     | Virtual Private Cloud (VPC)          | Private cloud networking                |
| IAM            | Identity and Access Management (IAM) | Control access and permissions          |
| Monitoring     | Cloud Monitoring                     | Monitor resources and applications      |
| Data analytics | BigQuery                             | Analyze large datasets                  |

Official documentation: [Google Cloud — Product Documentation](https://cloud.google.com/docs).

## 3. Comparison: GCP vs AWS vs Azure

| Feature              | Google Cloud        | AWS        | Microsoft Azure                 |
| -------------------- | ------------------- | ---------- | ------------------------------- |
| Virtual machines     | Compute Engine      | EC2        | Azure Virtual Machines          |
| Object storage       | Cloud Storage       | S3         | Blob Storage                    |
| Kubernetes           | GKE                 | EKS        | AKS                             |
| Serverless functions | Cloud Run functions | Lambda     | Azure Functions                 |
| Identity             | Cloud IAM           | AWS IAM    | Microsoft Entra ID / Azure RBAC |
| Monitoring           | Cloud Monitoring    | CloudWatch | Azure Monitor                   |

Remember: All three provide similar core cloud capabilities, but their services, pricing models, identity systems, and integrations differ.

Official documentation:

- [Google Cloud](https://cloud.google.com/docs)
- [AWS](https://docs.aws.amazon.com/)
- [Microsoft Azure](https://learn.microsoft.com/en-us/azure/)

## 4. GCP Global Infrastructure

Google Cloud operates a global infrastructure that allows applications to run close to their users and meet availability requirements.

- Regions: Geographical areas containing Google Cloud resources.
- Zones: Deployment areas within a region where resources such as VMs can run.
- Edge locations / edge points of presence: Locations used by Google's network to bring services such as content delivery closer to users.

Example: An application can run in the Mumbai region (`asia-south1`) to serve users in India with suitable latency.

Google Cloud global infrastructure

Region — Mumbai (`asia-south1`)

Zone A

VMs and workloads

Zone B

VMs and workloads

Zone C

VMs and workloads

Conceptual illustration; actual zones and service availability vary by region.

Important: Deploying across multiple zones can improve resilience, but does not automatically make an application highly available. The application and its dependencies must also be designed for failover.

Official documentation: [Google Cloud — Locations](https://cloud.google.com/about/locations).

## 5. Regions, Zones, and Edge Locations

| Term          | Meaning                                                           | Example                     |
| ------------- | ----------------------------------------------------------------- | --------------------------- |
| Region        | A geographical area                                               | Mumbai                      |
| Zone          | An isolated deployment area within a region                       | A zone within `asia-south1` |
| Edge location | A network location used to serve or route traffic closer to users | Google network edge         |

Easy way to remember: Region = geographical area, Zone = deployment location, Edge = closer network access.

Official documentation: [Google Cloud — Regions and Zones](https://cloud.google.com/compute/docs/regions-zones).

## 6. GCP Console Tour

The Google Cloud Console is a web-based interface for managing cloud resources.

Important sections:

- Dashboard: Overview of projects and resources.
- Project selector: Select or switch projects.
- Compute Engine: Create and manage VMs.
- Cloud Storage: Create buckets and manage objects.
- IAM & Admin: Manage users, roles, permissions, and projects.
- Billing: View costs, budgets, and billing accounts.
- Monitoring: View metrics, dashboards, and alerts.
  
Practice: Open the [Google Cloud Console](https://console.cloud.google.com/), select a project, open Cloud Shell, and run:


The commands list your active CLI configuration, accessible projects, and enabled services for the selected project, respectively.

Official documentation: [Google Cloud Shell documentation](https://cloud.google.com/shell/docs).

## 7. Creating and Setting Up GCP Accounts

A Google account lets you sign in to Google Cloud. Organizations can additionally use Google Workspace or Cloud Identity for centralized identity and administration.

Basic setup steps:

1. Sign in to [Google Cloud Console](https://console.cloud.google.com/).
2. Complete the requested account setup.
3. Set up a billing account and payment method if required.
4. Create or select a project.
5. Enable the APIs required by your application.
6. Assign appropriate IAM permissions.
7. Configure budgets and monitor usage.

Important: Some services require billing to be enabled. Free usage, trial credits, or free-tier allowances do not mean every service is free.

Official documentation: [Google Cloud — Get Started](https://cloud.google.com/docs/get-started).
