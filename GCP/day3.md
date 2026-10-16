
## 1. What is IAM?

IAM answers one question for every action in Google Cloud: **who can do what on which resource?**

Think of an office building:

- **Who** = the person with a badge (a *principal*: user, group, service account)
- **What** = the doors that badge opens (a *role*, which is a bundle of *permissions*)
- **Which resource** = the floor or room (a project, a bucket, a VM)
- The rule connecting them = an **IAM policy**

```
Principal  +  Role  +  Resource  =  Access
(who)         (what)    (where)
```

| Term | Plain meaning | Example |
| --- | --- | --- |
| Principal | The identity asking for access | `alice@gmail.com`, a group, a service account |
| Permission | One single allowed action | `storage.objects.get` |
| Role | A bundle of permissions | `roles/storage.objectViewer` |
| IAM policy | List of "principal has role" bindings on a resource | "Group devs = Viewer on project X" |
| Resource | The thing being protected | Project, bucket, VM, dataset |

> Allow policies are additive: nobody has access unless a role is given to them somewhere. (GCP also has advanced *deny* policies, which you can ignore while learning.)

---

## 2. Principals, Roles and Groups

### 2.1 Principals (the "who")

| Principal type | What it is | Format |
| --- | --- | --- |
| Google Account | A person's Gmail or Workspace login | `user:alice@example.com` |
| Service account | Identity for an app or VM | `serviceAccount:my-sa@my-project.iam.gserviceaccount.com` |
| Google Group | Collection of users and service accounts | `group:devs@example.com` |
| Domain | Everyone in your company domain | `domain:example.com` |
| `allAuthenticatedUsers` | Anyone signed in with a Google account (avoid) | `allAuthenticatedUsers` |
| `allUsers` | Anyone on the internet (avoid) | `allUsers` |

**Creating principals**

- Users and groups are **not** created inside IAM. Create them in **Cloud Identity** or **Google Workspace** (Admin console), or use existing Gmail accounts for learning.
- Service accounts are created inside GCP (see section 4).

### 2.2 Roles (the "what")

A role is a named bundle of permissions. Permissions follow the pattern `service.resource.verb`, for example `compute.instances.start`.

| Role type | Maintained by | Example | When to use |
| --- | --- | --- | --- |
| Basic | Google | `roles/viewer`, `roles/editor`, `roles/owner` | Learning only. Too broad for real work. |
| Predefined | Google | `roles/storage.objectViewer` | Best default for most cases. |
| Custom | You | `myBucketReader` | When nothing predefined fits and you need least privilege. |

See what a role contains:

```bash
gcloud iam roles describe roles/storage.objectViewer
```

Create a custom role:

```bash
gcloud iam roles create myBucketReader \
  --project=MY_PROJECT_ID \
  --title="My Bucket Reader" \
  --permissions=storage.buckets.get,storage.objects.get,storage.objects.list
```

### 2.3 Groups

Instead of giving roles to 20 individual users, put them in a **group** and give the role to the group once. New joiner: add them to the group. Someone leaves: remove them. Your policy stays short.

**Steps to create a group** (needs Cloud Identity or Google Workspace):

1. Open the Google Admin console, go to **Directory > Groups**.
2. Click **Create group**, give it a name and email (for example `gcp-developers@example.com`).
3. Add members.
4. In GCP, grant the group a role on a project (see section 3).

Name groups by **job function**, not by person: `gcp-developers`, `gcp-network-admins`, `gcp-billing-viewers`.

---

## 3. Managing IAM Policies

An IAM policy is attached to a resource and holds a list of **bindings**: *these members* get *this role*.

### 3.1 What a policy looks like

```json
{
  "bindings": [
    {
      "role": "roles/storage.objectViewer",
      "members": [
        "group:gcp-developers@example.com",
        "user:alice@example.com"
      ]
    },
    {
      "role": "roles/compute.instanceAdmin.v1",
      "members": [
        "serviceAccount:deploy-sa@my-project.iam.gserviceaccount.com"
      ]
    }
  ],
  "etag": "BwXyz123=",
  "version": 3
}
```

The `etag` protects against two people editing the policy at the same time.

### 3.2 Inheritance: access flows downward

```
Organization
 └── Folder
      └── Project
           └── Resource (bucket, VM, dataset ...)
```

A role granted at a higher level is **inherited** by everything below it.

- `Viewer` on a folder: can view every project inside it.
- `Viewer` on one bucket: can view only that bucket.
- You cannot remove inherited access at a lower level. Fix it where it was granted.

Grant access **as low as possible**.

### 3.3 Grant and remove access (Console)

1. Open the Cloud Console and select your project.
2. Go to **IAM & Admin > IAM**.
3. Click **Grant access**.
4. Enter the principal under **New principals**.
5. Choose a **Role**, click **Save**.

To remove: click the pencil (edit) icon next to the principal, then the trash icon next to the role.

### 3.4 Grant and remove access (gcloud)

```bash
# Grant
gcloud projects add-iam-policy-binding MY_PROJECT_ID \
  --member="group:gcp-developers@example.com" \
  --role="roles/storage.objectViewer"

# Remove
gcloud projects remove-iam-policy-binding MY_PROJECT_ID \
  --member="group:gcp-developers@example.com" \
  --role="roles/storage.objectViewer"

# View the policy
gcloud projects get-iam-policy MY_PROJECT_ID

# Grant on a single bucket
gcloud storage buckets add-iam-policy-binding gs://my-bucket \
  --member="user:alice@example.com" \
  --role="roles/storage.objectViewer"
```

### 3.5 Check and troubleshoot

- **Who has access?** `gcloud projects get-iam-policy`, or the IAM page.
- **Why can't this user do X?** Use the **Policy Troubleshooter** (IAM & Admin > Policy Troubleshooter).
- **Unused access?** The **IAM Recommender** suggests removing roles that were never used.
- Changes can take a few minutes to apply.




## Official Documentation

- [IAM overview](https://cloud.google.com/iam/docs/overview)
- [Roles and permissions](https://cloud.google.com/iam/docs/roles-overview)
- [Understanding allow policies](https://cloud.google.com/iam/docs/allow-policies)
- [Resource hierarchy](https://cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy)
- [Service accounts overview](https://cloud.google.com/iam/docs/service-account-overview)
- [Best practices for service accounts](https://cloud.google.com/iam/docs/best-practices-service-accounts)
