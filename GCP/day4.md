
## 4. Creating and Managing Service Accounts

### 4.1 What is a service account?

An identity for a **program or machine**, not a human. Its email looks like `NAME@PROJECT_ID.iam.gserviceaccount.com`.

It has two sides:

- It is a **principal** (it can be given roles).
- It is also a **resource** (others need permission to use or manage it).

### 4.2 Types

| Type | Created by | Notes |
| --- | --- | --- |
| User-managed | You | Use these. |
| Default | GCP | Often has broad Editor role. Avoid relying on it. |
| Google-managed (service agents) | Google | Used internally. Don't modify. |

### 4.3 Create

**Console:** IAM & Admin > Service Accounts > **Create service account** > enter a name > **Create and continue** > pick a role > **Done**.

**gcloud:**

```bash
gcloud iam service-accounts create my-app-sa \
  --display-name="My App Service Account"
```

### 4.4 Give it permissions

A new service account can do nothing until you grant it a role:

```bash
gcloud projects add-iam-policy-binding MY_PROJECT_ID \
  --member="serviceAccount:my-app-sa@MY_PROJECT_ID.iam.gserviceaccount.com" \
  --role="roles/storage.objectViewer"
```

### 4.5 Attach it to a workload

The safest way is to **attach** the service account to what runs your code (Compute Engine VM, Cloud Run, Cloud Function). Credentials are provided automatically, with no key file.

```bash
gcloud compute instances create my-vm \
  --service-account=my-app-sa@MY_PROJECT_ID.iam.gserviceaccount.com \
  --scopes=cloud-platform
```

### 4.6 Who may use the service account?

| Role on the service account | What it allows |
| --- | --- |
| `roles/iam.serviceAccountUser` | Attach it to a resource (like a VM) |
| `roles/iam.serviceAccountTokenCreator` | Impersonate it (act as it) |
| `roles/iam.serviceAccountAdmin` | Create, edit, delete service accounts |
| `roles/iam.serviceAccountKeyAdmin` | Create and delete its keys |

Whoever can act as a powerful service account effectively has its power.

### 4.7 Keys: avoid them if you can

A key is a JSON file that works like a password. Safer options, in order:

1. **Attached service account** (no key) for code on GCP.
2. **Impersonation** for humans or tools acting as the account temporarily.
3. **Workload Identity Federation** for code outside GCP (GitHub Actions, AWS).
4. **Key files** only as a last resort. Rotate and delete them.

### 4.8 Manage and clean up

```bash
gcloud iam service-accounts list
gcloud iam service-accounts disable my-app-sa@MY_PROJECT_ID.iam.gserviceaccount.com
gcloud iam service-accounts delete my-app-sa@MY_PROJECT_ID.iam.gserviceaccount.com
```

Tip: disable first, wait a few days to see if anything breaks, then delete.

---

## 5. Hands-on Example: Configure a Service Account

**Goal:** create a service account that can only **read** files from one bucket, and prove it cannot write.

**Prerequisites**

- A Google Cloud project with billing enabled (a free-tier trial is fine)
- The [gcloud CLI](https://cloud.google.com/sdk/docs/install) installed, or use **Cloud Shell** in the console (nothing to install)

### Step 1: Log in and set variables

```bash
gcloud auth login

export PROJECT_ID="your-project-id"
export BUCKET="${PROJECT_ID}-iam-demo"
export SA_NAME="bucket-reader-sa"
export SA_EMAIL="${SA_NAME}@${PROJECT_ID}.iam.gserviceaccount.com"
export MY_EMAIL="$(gcloud config get-value account)"

gcloud config set project $PROJECT_ID
```

### Step 2: Create a bucket with a test file

```bash
gcloud storage buckets create gs://$BUCKET --location=asia-south1
echo "hello iam" > hello.txt
gcloud storage cp hello.txt gs://$BUCKET/
```

### Step 3: Create the service account

```bash
gcloud iam service-accounts create $SA_NAME \
  --display-name="Bucket Reader SA"

gcloud iam service-accounts list
```

### Step 4: Give it a read-only role on that one bucket

Grant at the **bucket level**, not the project, to keep access as low as possible.

```bash
gcloud storage buckets add-iam-policy-binding gs://$BUCKET \
  --member="serviceAccount:${SA_EMAIL}" \
  --role="roles/storage.objectViewer"
```

### Step 5: Allow yourself to impersonate it (no key file needed)

```bash
gcloud iam service-accounts add-iam-policy-binding $SA_EMAIL \
  --member="user:${MY_EMAIL}" \
  --role="roles/iam.serviceAccountTokenCreator"
```

Wait about a minute for the change to apply.

### Step 6: Test it

Reading should **work**:

```bash
gcloud storage ls gs://$BUCKET \
  --impersonate-service-account=$SA_EMAIL

gcloud storage cat gs://$BUCKET/hello.txt \
  --impersonate-service-account=$SA_EMAIL
```

Writing should **fail** with a 403 permission error:

```bash
gcloud storage cp hello.txt gs://$BUCKET/new.txt \
  --impersonate-service-account=$SA_EMAIL
```

That failure is correct: the account has a viewer role, not a writer role. This is least privilege in action.

### Step 7 (optional): Use it from a VM

```bash
gcloud compute instances create iam-demo-vm \
  --zone=asia-south1-a \
  --service-account=$SA_EMAIL \
  --scopes=cloud-platform
```

Code running on that VM gets the service account's access automatically, with no key file.

### Step 8: Check the policy

```bash
gcloud storage buckets get-iam-policy gs://$BUCKET
```

You should see `serviceAccount:bucket-reader-sa@...` listed under `roles/storage.objectViewer`.

### Step 9: Clean up

```bash
gcloud compute instances delete iam-demo-vm --zone=asia-south1-a   # only if you created it
gcloud storage rm -r gs://$BUCKET
gcloud iam service-accounts delete $SA_EMAIL
rm hello.txt
```

### Common errors

| Error | Likely cause | Fix |
| --- | --- | --- |
| `403 ... does not have storage.objects.get` | Role missing, or still propagating | Check the binding; wait a minute |
| `Failed to impersonate` | You lack Token Creator on the service account | Repeat Step 5 |
| `409 bucket already exists` | Bucket names are globally unique | Change `$BUCKET` |
| `Permission denied to create service account` | You lack `roles/iam.serviceAccountAdmin` | Ask a project admin |

---

## 6. IAM Best Practices

The big idea is **least privilege**: only the access needed, on only the resources needed, for only as long as needed.

| # | Practice | Why it matters |
| --- | --- | --- |
| 1 | Use least privilege | Limits damage if an account is misused |
| 2 | Avoid basic roles (Owner, Editor, Viewer) in real projects | Too many permissions across all services |
| 3 | Prefer predefined roles; custom only when needed | Google keeps predefined roles updated |
| 4 | Grant roles to groups, not individuals | Easy onboarding and offboarding |
| 5 | Grant at the lowest level | Inherited access can't be removed lower down |
| 6 | One service account per application | A leak affects only one app |
| 7 | Avoid service account keys | Keys can leak and don't expire by default |
| 8 | Don't use default service accounts for real workloads | They often have broad Editor access |
| 9 | Never grant `allUsers` or `allAuthenticatedUsers` unless truly public | Opens the resource to the internet |
| 10 | Separate duties | Reduces mistakes and fraud |
| 11 | Turn on 2-Step Verification for humans | Protects against stolen passwords |
| 12 | Review access regularly (IAM Recommender, Policy Analyzer) | Removes unused permissions |
| 13 | Enable and monitor Cloud Audit Logs | Shows who did what and when |
| 14 | Keep few Owners (2 or 3, ideally a group) | Owners can change anything |
| 15 | Use clear naming for groups and service accounts | Purpose is obvious |

**Checklist before granting any role**

- [ ] Is this the smallest role that works?
- [ ] Am I granting it to a group or service account instead of one person?
- [ ] Is it at the lowest possible level?
- [ ] Do I know when this access should be reviewed or removed?

---

## 7. Cheat Sheet

| Task | Command |
| --- | --- |
| Show a project's IAM policy | `gcloud projects get-iam-policy PROJECT_ID` |
| Grant a role | `gcloud projects add-iam-policy-binding PROJECT_ID --member=MEMBER --role=ROLE` |
| Remove a role | `gcloud projects remove-iam-policy-binding PROJECT_ID --member=MEMBER --role=ROLE` |
| See what a role contains | `gcloud iam roles describe ROLE` |
| List roles | `gcloud iam roles list` |
| Create a custom role | `gcloud iam roles create ID --project=PROJECT_ID --permissions=P1,P2` |
| Create a service account | `gcloud iam service-accounts create NAME` |
| List service accounts | `gcloud iam service-accounts list` |
| Disable / delete a service account | `gcloud iam service-accounts disable EMAIL` / `delete EMAIL` |
| Run a command as a service account | add `--impersonate-service-account=EMAIL` |

**Remember these five lines**

1. IAM = who (principal) can do what (role) on which resource.
2. Roles are bundles of permissions; prefer predefined over basic.
3. Give access to groups, at the lowest level that works.
4. Service accounts are identities for apps; avoid downloading keys.
5. Least privilege, and review access regularly.

---

##  Official Documentation

- [Service accounts overview](https://cloud.google.com/iam/docs/service-account-overview)
- [Best practices for service accounts](https://cloud.google.com/iam/docs/best-practices-service-accounts)
- [Service account impersonation](https://cloud.google.com/iam/docs/service-account-impersonation)
- [Workload Identity Federation](https://cloud.google.com/iam/docs/workload-identity-federation)
- [Using IAM securely (best practices)](https://cloud.google.com/iam/docs/using-iam-securely)
- [Policy Troubleshooter](https://cloud.google.com/policy-intelligence/docs/troubleshoot-access)
- [gcloud IAM reference](https://cloud.google.com/sdk/gcloud/reference/iam)
