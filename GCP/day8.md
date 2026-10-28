# GCP Cloud Notes: Filestore and Cloud SQL

These notes cover the four topics in simple language, with real-life examples, practical steps, best practices, and official Google Cloud documentation links.

## 1. Understanding Filestore (NFS on Cloud)

### What is Filestore?

Google Cloud Filestore is a managed file storage service that allows multiple virtual machines (VMs) or applications to access the same files over a network using NFS (Network File System).

Instead of storing files separately on each VM, you can store them in one shared location.

[Filestore: Share File Server พร้อมใช้งานบน Google Cloud Platform | by Nichapat H. | Medium](https://images.openai.com/static-rsc-4/faG7Q8l4zfkbbCGLCwP9YnVdwiSfOkE9amNamjrWFpwsRXE68YiLUoR_ptGgL3sKBwwvrKkg7osAjBOS47MKqplGaXvi3cGoMjghSOk0EFO5MTjIhvOIqDwOyUMRSrTPvJjwoZtY0yo7Li2nVxb-LTwikE9Oo8Dj3ZQxkcwPtGI?purpose=inline)


### Real-life example

Imagine three web servers running an application:

- VM1 uploads a document.
- VM2 needs to read the same document.
- VM3 also needs access to that document.

If the file is stored locally on VM1, the other VMs cannot automatically access it. With Filestore, all three VMs can access the same shared directory.

### How Filestore works

1. Create a Filestore instance.
2. Connect it to a VPC network.
3. Obtain its IP address and NFS share path.
4. Mount the shared directory on your VMs.
5. Read and write files from the shared directory.

### Key concepts

| Concept            | Explanation                                                 |
| ------------------ | ----------------------------------------------------------- |
| NFS                | Protocol used to share files over a network.                |
| Filestore instance | Managed file storage resource.                              |
| File share         | Shared directory that stores files.                         |
| VPC network        | Network used for connectivity between resources.            |
| Mount              | Making a remote file share accessible as a local directory. |
| Capacity           | Storage space available for the file share.                 |

### Filestore vs. other storage services

| Service         | Primary purpose                                           |
| --------------- | --------------------------------------------------------- |
| Filestore       | Shared file storage using NFS.                            |
| Cloud Storage   | Object storage for images, backups, logs, and data lakes. |
| Persistent Disk | Block storage attached to VMs.                            |
| Cloud SQL       | Managed relational database service.                      |

When should you use Filestore?

- Shared application files across multiple VMs.
- Shared content for web servers.
- Applications requiring NFS-compatible storage.
- Workloads that need a shared filesystem.

Official documentation: [Google Cloud Filestore overview](https://cloud.google.com/filestore/docs/overview?utm_source=chatgpt.com)

## 2. Deploying and Configuring Filestore

In this practical, we will create a Filestore instance and mount it on a Linux VM.

### Prerequisites

- A Google Cloud project with billing enabled.
- A VPC network and a Linux VM.
- Appropriate IAM permissions.
- Network connectivity between the VM and Filestore.

### Step 1: Open Filestore

1. Open the [Google Cloud Console](https://console.cloud.google.com/?utm_source=chatgpt.com).
2. Search for Filestore.
3. Open the Filestore page.
4. Click Create instance.

### Step 2: Configure the instance

Example configuration:

| Setting         | Example                                      |
| --------------- | -------------------------------------------- |
| Instance ID     | `training-filestore`                         |
| Region          | `asia-south1` (Mumbai)                       |
| Zone            | Select a supported zone                      |
| Tier            | Basic, if suitable for the lab               |
| Capacity        | Choose an allowed size for the selected tier |
| Network         | `default` or your custom VPC                 |
| File share name | `vol1`                                       |

The available tiers, capacity ranges, and features depend on the selected Filestore configuration.

Important: Your VM and Filestore must have suitable network connectivity. Filestore does not require a public IP address.

### Step 3: Resolve the service connection policy error

If you see this message:

> The selected network (default) does not have a required service connection policy to connect with this Filestore instance.

This generally indicates a missing network connectivity prerequisite for the selected Filestore configuration.

Try the following:

1. Confirm the selected region and Filestore tier.
2. Check the error details and the relevant networking requirements for that configuration.
3. If the selected tier requires a service connection policy, create the required policy in the appropriate region and VPC using the documented setup.
4. Retry instance creation after the policy is ready.

Do not assume that changing the VPC alone will fix the issue. Some configurations have additional networking prerequisites.

Official guide: [Filestore networking and firewall configuration](https://cloud.google.com/filestore/docs/configuring-firewall?utm_source=chatgpt.com) and [Filestore documentation and setup guides](https://cloud.google.com/filestore/docs?utm_source=chatgpt.com).

### Step 4: Get the Filestore IP address

After the instance is created:

1. Open the Filestore instance details.
2. Note the IP address and file share name.

Example:

```
Filestore IP: 10.10.0.5
File share:   vol1
```

These are example values. Use the actual values from your instance.

### Step 5: Install the NFS client on your Linux VM

For Ubuntu or Debian:

```
sudo apt update
sudo apt install -y nfs-common
```

For RHEL-based Linux:

```
sudo dnf install -y nfs-utils
```

### Step 6: Mount the Filestore share

Create a mount directory:

```
sudo mkdir -p /mnt/filestore
```

Mount the share:

```
sudo mount -t nfs 10.10.0.5:/vol1 /mnt/filestore
```

Verify the mount:

```
df -h
mount | grep nfs
```

### Step 7: Test shared storage

Create a file:

```
sudo touch /mnt/filestore/test.txt
echo "Hello from Filestore" | sudo tee /mnt/filestore/test.txt
```

Read it:

```
cat /mnt/filestore/test.txt
```

If another VM mounts the same share, it can access that file too, subject to permissions.

### Step 8: Configure automatic mounting (optional)

Edit `/etc/fstab`:

```
sudo nano /etc/fstab
```

Add the following line using your actual IP and share name:

```
10.10.0.5:/vol1 /mnt/filestore nfs defaults,_netdev 0 0
```

Test the configuration:

```
sudo mount -a
```

Best practices

- Restrict NFS access to trusted networks and VMs.
- Configure the necessary firewall rules and routes.
- Use appropriate file and directory permissions.
- Choose the correct Filestore tier and capacity for the workload.
- Test automatic mounting before relying on it in production.
