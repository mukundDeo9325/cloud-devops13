## Lifecycle Management Rules

### What is Lifecycle Management?
<cite index="19-1">By using blob lifecycle management, customers can proactively optimize costs by implementing rule-based policies that automatically transition data to cooler tiers or expire it when it's no longer needed — ensuring data is always stored in the most cost-effective manner.</cite>

### How Policies Work
<cite index="19-1">A lifecycle management policy is a collection of rules in a JSON document</cite>, and <cite index="19-1">policies are supported for block and append blobs in general-purpose v2, premium block blob, and Blob Storage accounts</cite> (note: <cite index="19-1">lifecycle management doesn't affect system containers like `$logs` or `$web`</cite>).

**A rule typically defines:**
1. **Filters** — which blobs the rule applies to (e.g., name prefix, blob type)
2. **Conditions** — time-based triggers (e.g., "not modified in 30 days")
3. **Actions** — what happens when the condition is met (tier change or delete)

### Sample Policy — Tiering
```json
{
  "rules": [
    {
      "name": "moveToCoolThenArchive",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": { "blobTypes": ["blockBlob"], "prefixMatch": ["logs/"] },
        "actions": {
          "baseBlob": {
            "tierToCool": { "daysAfterModificationGreaterThan": 30 },
            "tierToArchive": { "daysAfterModificationGreaterThan": 90 },
            "delete": { "daysAfterModificationGreaterThan": 365 }
          }
        }
      }
    }
  ]
}
```
This automatically: moves `logs/` blobs to **Cool** after 30 days → **Archive** after 90 days → **deletes** them after 365 days — with **zero manual intervention**.

### Important Behavioral Facts
- <cite index="20-1">A lifecycle policy will NOT delete the current version of a blob until any previous versions or snapshots associated with it have been deleted</cite> — you must explicitly include versions/snapshots in a delete action if they exist
- <cite index="23-1">Lifecycle management can take up to 24 hours to begin processing after a rule is added, edited, or after the prior run completes</cite>
- <cite index="24-1">If soft-delete is enabled, a lifecycle policy that "deletes" a blob actually puts it into a soft-deleted state, retained for the configured retention period rather than deleted permanently</cite>
- <cite index="23-1">Avoid moving very small objects to cooler tiers — the transaction cost of moving them may exceed the savings</cite>

### How to Configure
- **Portal:** <cite index="22-1">Storage account → Data management → Lifecycle Management → Add a rule, configure scope/blob type, and set base blob conditions (e.g., move to cool if not modified for 30 days)</cite>
- **PowerShell:** <cite index="22-1">Use `Add-AzStorageAccountManagementPolicyAction`, `New-AzStorageAccountManagementPolicyFilter`, `New-AzStorageAccountManagementPolicyRule`, and `Set-AzStorageAccountManagementPolicy`</cite>
- **CLI / ARM Templates:** policies can be applied as raw JSON via `az storage account management-policy create`

📘 **Official Docs:**
- [Azure Blob Storage lifecycle management overview](https://learn.microsoft.com/en-us/azure/storage/blobs/lifecycle-management-overview)
- [Configure a lifecycle management policy](https://learn.microsoft.com/en-us/azure/storage/blobs/lifecycle-management-policy-configure)

### 🧪 Practice Lab
1. Save the sample JSON above as `lifecycle-policy.json` in Cloud Shell.
2. Apply it to your storage account:
   ```bash
   az storage account management-policy create \
     --account-name <your-account-name> \
     --resource-group rg-storage-day7 \
     --policy @lifecycle-policy.json
   ```
3. View the applied policy:
   ```bash
   az storage account management-policy show --account-name <your-account-name> --resource-group rg-storage-day7
   ```
4. In the Portal, go to **Lifecycle Management** for your account and confirm the rule appears in **List View**.
5. Discuss: design a lifecycle policy (on paper) for a company's **CCTV footage** — footage needs fast access for 7 days, occasional access for 60 days, must be retained for legal reasons for 2 years, then deleted.

---

## Azure Storage Explorer Demo

### What is Azure Storage Explorer?
<cite index="29-1">Microsoft Azure Storage Explorer is a standalone app that makes it easy to work with Azure Storage data on Windows, macOS, and Linux.</cite> It's a **free GUI desktop application** — essentially a "File Explorer" for all your Azure Storage resources.

### What You Can Do With It
<cite index="27-1">Upload, download, and manage Azure Storage blobs, files, queues, and tables, as well as Azure Data Lake Storage entities and Azure managed disks — configure storage permissions, access controls, tiers, and rules, and connect and manage storage accounts and resources across subscriptions and organizations.</cite>

### Why Use It (vs Portal/CLI)?
| Scenario | Why Storage Explorer Helps |
|---|---|
| Managing **multiple storage accounts** across subscriptions | One unified interface, no repeated portal navigation |
| **Bulk upload/download** of files | Drag-and-drop, faster than manual portal upload |
| **Generating SAS tokens visually** | Built-in dialog to set permissions/expiry without memorizing CLI syntax |
| Browsing **Table/Queue** data | Visual grid/table view, easier than raw JSON via CLI |
| Working with **local emulators** | Can also connect to Azurite (local storage emulator) for development/testing without touching real cloud resources |

### Typical Demo Flow
1. **Connect** — sign in with an Azure account (or connect via connection string / SAS URI / access key for accounts you don't own but have credentials for)
2. **Browse** — expand Subscription → Storage Account → Blob Containers / File Shares / Queues / Tables
3. **Create a container** and **upload a blob** — right-click → Create Blob Container, then drag a file in
4. **Set access tier** — right-click a blob → Change Tier
5. **Generate a SAS** — right-click a container/blob → Get Shared Access Signature → set permissions & expiry
6. **Manage access policies** — set stored access policies at the container level for reusable, revocable permission sets

📘 **Official Docs:**
- [Get started with Storage Explorer – Microsoft Learn](https://learn.microsoft.com/en-us/azure/storage/storage-explorer/vs-azure-tools-storage-manage-with-storage-explorer)
- [Quickstart: Create a blob with Azure Storage Explorer – Microsoft Learn](https://learn.microsoft.com/en-us/azure/storage/blobs/quickstart-storage-explorer)
- [Download Azure Storage Explorer](https://azure.microsoft.com/en-us/products/storage/storage-explorer/)

### 🧪 Practice Lab (Demo Walkthrough)
1. Download and install **Azure Storage Explorer** from the official link above.
2. Sign in with your Azure account and select your subscription.
3. Navigate to the storage account from this lesson (`stday7demo...`) → expand **Blob Containers**.
4. Right-click → **Create Blob Container** → name it `demo-explorer`.
5. Drag-and-drop a local file into the container to upload it.
6. Right-click the uploaded file → **Get Shared Access Signature** → set a 1-hour expiry, Read-only permission → **Create** → copy the generated SAS URL.
7. Open that SAS URL in a private/incognito browser tab to confirm it downloads the file without needing to be signed into Azure.
8. Clean up all lab resources:
   ```bash
   az group delete --name rg-storage-day7 --yes
   ```
