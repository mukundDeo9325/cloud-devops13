# Day 6 

## Understanding Storage Accounts

### What is a Storage Account?
<cite index="159-1">A storage account contains all of your Azure Storage data objects — blobs, files, queues, and tables. It provides a unique namespace for your data that's accessible from anywhere in the world over HTTP or HTTPS.</cite> Think of it as the top-level "container" that everything else (blob containers, file shares, etc.) lives inside.

### Storage Account Types
- https://learn.microsoft.com/en-us/azure/storage/common/storage-account-overview
### Key Building Blocks
```
Storage Account
   ├── Blob Storage → Containers → Blobs (files)
   ├── Azure Files → File Shares → Files/Folders
   ├── Queue Storage → Queues → Messages
   └── Table Storage → Tables → Entities (NoSQL rows)
```

### Naming & Access Rules
- Storage account names must be **globally unique**, lowercase letters/numbers only, 3-24 characters
- Every storage account gets a unique endpoint per service, e.g.:
  - Blob: `https://<account>.blob.core.windows.net`
  - Files: `https://<account>.file.core.windows.net`
- <cite index="155-1">Every request to Azure Storage must be authorized — supported methods include Microsoft Entra ID integration (recommended, via Azure RBAC) for Blob, File, Table, and Queue data</cite>

📘 **Official Docs:** [Storage account overview – Microsoft Learn](https://learn.microsoft.com/en-us/azure/storage/common/storage-account-overview)

### 🧪 Practice Lab
1. In Cloud Shell (Bash), create a resource group and a **GPv2** storage account:
   ```bash
   az group create --name rg-storage-day6 --location eastus

   az storage account create \
     --name stday6demo$RANDOM \
     --resource-group rg-storage-day6 \
     --location eastus \
     --sku Standard_LRS \
     --kind StorageV2
   ```
2. List the account's endpoints:
   ```bash
   az storage account show --name <your-account-name> --resource-group rg-storage-day6 --query "primaryEndpoints" --output json
   ```
3. Note the different endpoints created for blob, file, queue, and table — even though you haven't uploaded anything yet.

---

## 2. Blob Storage: Containers, Access Tiers, Static Website Hosting

### Containers and Blobs
- A **container** is like a folder — it organizes blobs (files) and sets the access level for everything inside it
- A **blob** is the actual object/file (text, image, video, binary data, etc.)
- Azure supports 3 blob types: **Block blobs** (most common — text/binary files), **Append blobs** (optimized for append-only operations like logs), **Page blobs** (random read/write — used for VHD/VM disks)

### Access Tiers (Cost vs. Performance Trade-off)
<cite index="165-1">Azure Storage access tiers let you store blob data in the most cost-effective manner based on how it's being used.</cite>

| Tier | Type | Best For | Minimum Retention |
|---|---|---|---|
| **Hot** | <cite index="165-1">Online — optimized for data accessed or modified frequently. Highest storage cost, lowest access cost.</cite> | Active/frequently used data | None |
| **Cool** | <cite index="165-1">Online — optimized for infrequently accessed/modified data. Lower storage cost, higher access cost than hot.</cite> | Short-term backup, infrequent access | <cite index="165-1">30 days</cite> |
| **Cold** | <cite index="165-1">Online — optimized for data rarely accessed/modified but still requiring fast retrieval. Lower storage cost, higher access cost than cool.</cite> | Rarely accessed but still needs quick access | <cite index="165-1">90 days</cite> |
| **Archive** | <cite index="167-1">Offline tier optimized for data that's rarely accessed and has flexible latency requirements</cite> | Long-term compliance/archival data | <cite index="167-1">180 days</cite> |

> ⚠️ **Important:** <cite index="166-1">Setting the tier from Archive back to Hot or Cool typically takes up to 15 hours to complete</cite> — the data must be "rehydrated" before it's usable again. Moving data out of a tier before its minimum retention period triggers an **early deletion penalty**.

**Setting tiers:** <cite index="171-1">Hot and Cool tiers can be set at the storage-account level; Hot, Cool, Cold, and Archive can also be set at the individual blob level — but Archive is only available at the blob level.</cite>

### Static Website Hosting
<cite index="185-1">Because Blob Storage provides static website hosting support, it's a great option when you don't need a web server to render content — though you're limited to hosting static content like HTML, CSS, JavaScript, and image files.</cite>

**How it works:**
- <cite index="185-1">A blob container named `$web` is automatically created within the storage account; you add your website's files to this `$web` container to make them accessible through the static website's primary endpoint.</cite>
- <cite index="185-1">You can enable static website hosting free of charge — you're billed only for the blob storage your site uses and its operation costs.</cite>
- For custom headers or advanced routing, Microsoft recommends **Azure CDN** or **Azure Static Web Apps** as a more full-featured alternative.

📘 **Official Docs:**
- [Access tiers for blob data – Microsoft Learn](https://learn.microsoft.com/en-us/azure/storage/blobs/access-tiers-overview)
- [Static website hosting in Azure Storage – Microsoft Learn](https://learn.microsoft.com/en-us/azure/storage/blobs/storage-blob-static-website)


## Azure Files

### What is Azure Files?
<cite index="188-1">Azure Files provides fully managed file shares in the cloud that you can access through the Server Message Block (SMB) protocol, Network File System (NFS) protocol, and the Azure Files REST API — mountable concurrently from cloud or on-premises deployments.</cite>


### Access Options / Networking
- **Public endpoint** — accessible over the internet (with authentication/firewall rules)
- **Private endpoint** — <cite index="187-1">gives your storage account a private, static IP address within your virtual network, keeping traffic within peered virtual networks and avoiding connectivity interruptions from dynamic IPs</cite>
- **Service endpoint** — <cite index="192-1">restricts access to specific subnets without needing a static private IP, at no extra charge</cite>

📘 **Official Docs:**
- [Introduction to Azure Files – Microsoft Learn](https://learn.microsoft.com/en-us/azure/storage/files/storage-files-introduction)
- [SMB file shares in Azure Files – Microsoft Learn](https://learn.microsoft.com/en-us/azure/storage/files/files-smb-protocol)

## 4. Storage Redundancy Types

### Why Redundancy Matters
Azure Storage automatically keeps multiple copies of your data to protect against hardware failures, datacenter outages, and regional disasters. <cite index="176-1">Redundancy protects against hardware failure, not against data-modifying operations (like accidental deletes) — that's what soft delete/versioning/backup are for.</cite>

### The Redundancy Options

| Option | Copies & Location | Protects Against |
|---|---|---|
| **LRS** (Locally Redundant Storage) | <cite index="177-1">3 copies within 1 datacenter</cite> | Single hardware/disk failure |
| **ZRS** (Zone-Redundant Storage) | <cite index="174-1">Copy replicated synchronously across 3 separate availability zones within the same region</cite> | Datacenter-level outage |
| **GRS** (Geo-Redundant Storage) | <cite index="177-1">6 copies across 2 regions (3 in primary via LRS, 3 in secondary via LRS)</cite> | Entire primary region outage |
| **GZRS** (Geo-Zone-Redundant Storage) | <cite index="176-1">Data copied synchronously across 3+ availability zones in the primary region using ZRS, then copied asynchronously to a secondary region using LRS</cite> | Zone outage AND regional disaster |
| **RA-GRS / RA-GZRS** (Read-Access variants) | Same as GRS/GZRS | Same, **plus** <cite index="176-1">read access to the secondary region even when the primary is healthy</cite> |

### Key Facts to Teach
- <cite index="176-1">With GRS or GZRS alone, the secondary region's data is NOT available for read/write unless there's an actual failover — to get read access at any time, you need RA-GRS or RA-GZRS.</cite>
- <cite index="174-1">During an unplanned failover with geo-redundant storage, DNS entries are automatically updated so the secondary region's endpoints become the new primary endpoints, and clients can begin writing there.</cite>
- <cite index="179-1">ZRS is a good default for analytics workloads because it offers more redundancy than LRS while remaining fully compatible with analytics frameworks.</cite>
- <cite index="180-1">Microsoft recommends GZRS for applications requiring maximum consistency, durability, and disaster recovery resilience</cite> — though the primary region must support both availability zones and have a paired region.

### Simple Decision Guide
```
Dev/test, cost is priority, single-datacenter risk acceptable?
        → LRS

Need protection from a datacenter outage, single-region is fine?
        → ZRS

Need protection from an entire region going down?
        → GRS (write access to secondary only after failover)

Need protection from a region going down AND
constant read access to a backup copy?
        → RA-GRS or RA-GZRS (RA-GZRS = strongest option)
```

📘 **Official Docs:** [Azure Storage redundancy – Microsoft Learn](https://learn.microsoft.com/en-us/azure/storage/common/storage-redundancy)

##  Azure Storage Types & Access Tier Types (Summary)

### Storage Types (What You Can Store)

| Storage Type | What It Stores | Access Method |
|---|---|---|
| **Blob Storage** | Unstructured data — text, images, video, binaries | REST API, SDKs, Portal |
| **Azure Files** | Managed file shares (folders/files) | SMB, NFS, REST API |
| **Queue Storage** | Messages for async communication between app components | REST API, SDKs |
| **Table Storage** | NoSQL structured/semi-structured key-value data | REST API, SDKs |
| **Disk Storage** | Managed disks for VMs (covered on Day 4) | Attached to VMs only |

### Access Tier Types (Recap — Blob-Specific)

| Tier | Cost Profile | Access Speed |
|---|---|---|
| **Hot** | Highest storage cost, lowest access cost | Immediate |
| **Cool** | Lower storage, higher access cost | Immediate (30-day min retention) |
| **Cold** | Even lower storage, higher access cost than Cool | Immediate (90-day min retention) |
| **Archive** | Lowest storage cost, highest access cost | Hours (rehydration required, 180-day min retention) |

> 💡 There's also a **Smart Tier** option that <cite index="165-1">automatically moves your data between the hot, cool, and cold access tiers based on usage patterns, optimizing your costs automatically.</cite>

### Putting It All Together — A Real Scenario
A media company stores:
- **Active video projects** → Blob Storage, **Hot tier**, GZRS (need speed + resilience)
- **Completed projects kept for client review (3 months)** → Blob Storage, **Cool tier**, GRS
- **Legal compliance archive (7 years)** → Blob Storage, **Archive tier**, GRS
- **Shared team script/config files edited by editors on Windows machines** → **Azure Files (SMB)**, ZRS
- **Order/task processing between microservices** → **Queue Storage**

📘 **Official Docs:**
- [Introduction to Azure Storage – Microsoft Learn](https://learn.microsoft.com/en-us/azure/storage/common/storage-introduction)
- [Choose an Azure storage service – Azure Architecture Center](https://learn.microsoft.com/en-us/azure/architecture/guide/technology-choices/storage-options)
