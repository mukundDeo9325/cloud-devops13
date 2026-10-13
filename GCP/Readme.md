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

## 8. Creating and Organizing Projects

A project is the basic organizing unit in Google Cloud. It contains resources such as virtual machines, storage buckets, databases, and networks.

Steps to create a project:

1. Open Google Cloud Console.
2. Go to IAM & Admin → Manage Resources.
3. Click Create Project.
4. Enter the project name and project ID.
5. Select the parent organization or folder, if applicable.
6. Create the project and link a billing account when needed.

#### Heare Naming Followed as
```
## Project Naming
[your_purpose]-[Kind_of_project]-[ENV]
```
Example project structure:

- `dev-project` — development resources
- `test-project` — testing resources
- `prod-project` — production resources

Projects help separate workloads, IAM permissions, enabled APIs, and resource management.

Official documentation: [Google Cloud — Creating and Managing Projects](https://cloud.google.com/resource-manager/docs/creating-managing-projects).

## 9. Understanding Organizations, Folders, and Projects

Your attached photos explain the Google Cloud resource hierarchy.

![](./images/Hiarchy.png)

- Organization: Top-level container for a company. It enables centralized management.
- Folders: Optional containers that group projects and other folders.
- Projects: Organize resources and provide a boundary for permissions, APIs, and billing association.
- Resources: Actual cloud services used by applications.

## Extras

- Resource separation: Resources in separate projects are logically separated. Cross-project access can be configured through IAM and service-specific settings.
- Policy inheritance: Applicable IAM and organization policies can flow from an organization to folders and projects. The exact effect depends on the policy type and configuration.
- IAM: Permissions can be granted at different hierarchy levels. Granting access at a higher level can provide access to multiple child resources.
- Billing: Costs can be analyzed by project and grouped by folder or organization.

Example: An IT folder can contain `dev-project` and `prod-project`. Policies assigned at the folder level may apply to both projects.

Correction to remember: A project generally links to a Cloud Billing account; it does not normally have its own separate billing account. One billing account can pay for multiple projects.

Official documentation:

- [Google Cloud — Resource Hierarchy](https://cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy)
- [Google Cloud — IAM and Resource Hierarchy](https://cloud.google.com/iam/docs/resource-hierarchy-access-control)

## 10. Billing Account Setup and Configuration

A Cloud Billing account tracks charges and pays for the Google Cloud projects linked to it.

Basic setup:

1. Open the Billing section in Google Cloud Console.
2. Create or select a Cloud Billing account.
3. Configure the required payment profile and payment method.
4. Link the billing account to the relevant project.
5. Assign billing roles only to authorized users.
6. Review cost reports regularly.

Remember the difference:

| Item            | Purpose                                |
| --------------- | -------------------------------------- |
| Project         | Organizes resources                    |
| Billing account | Pays for linked projects               |
| Payment profile | Holds payment and customer information |
| Billing report  | Shows usage and costs                  |

Official documentation: [Google Cloud — Manage Billing Accounts](https://cloud.google.com/billing/docs/how-to/manage-billing-account).

---
