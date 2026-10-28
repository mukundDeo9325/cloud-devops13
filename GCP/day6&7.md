# GCP Compute Engine Notes

Compute Engine is Google Cloud's service for running virtual machines (VMs). These notes cover eight topics in simple words, each with its official doc link.

> Only the machine families section (7) was checked against an official page I fully opened. Sections 5, 6 and 8 follow page titles and general knowledge, so check the linked pages for exact current limits and prices.

## Table of Contents

1. [Understanding Compute Engine](#1-understanding-compute-engine)
2. [Launching and configuring VM instances](#2-launching-and-configuring-vm-instances)
3. [Custom images, snapshots, and disks](#3-custom-images-snapshots-and-disks)
4. [SSH and startup scripts](#4-ssh-and-startup-scripts)
5. [Google Cloud Marketplace](#5-google-cloud-marketplace)
6. [Volume (disk) types](#6-volume-disk-types-in-gcp)
7. [Instance (machine) family types](#7-instance-machine-family-types)
8. [Purchase (provisioning) types](#8-purchase-provisioning-types)
9. [Sources](#sources)

---

## 1. Understanding Compute Engine

Compute Engine (GCE) lets you rent virtual machines that run on Google's data centers, so you don't buy or maintain any hardware. It is Google Cloud's IaaS (Infrastructure as a Service) offering, and you are billed only for what you use.

### Key terms in easy words

| Term | Meaning |
| --- | --- |
| Project | The folder that holds all your resources and your bill |
| Region / Zone | A region is a location (like Mumbai, `asia-south1`); a zone is one data center area inside it (`asia-south1-a`) |
| Machine type | How many vCPUs and how much memory the VM gets |
| Image | The operating system template used to create the boot disk |
| Disk | The storage attached to the VM |
| VPC network | The private network the VM sits in |
| Service account | The identity the VM uses to call other Google Cloud services |
| Metadata | Key-value settings attached to a VM (startup scripts, SSH keys) |

### Why people use it

- Full control: choose OS, CPU, memory, disk and network yourself.
- Scale: group VMs into managed instance groups (MIGs) that add or remove VMs automatically.
- Live migration: Google can move a running VM to another host during maintenance, without a reboot.
- Pay for use: VMs are billed per second, with a one-minute minimum.

**Official docs:** [Compute Engine overview](https://docs.cloud.google.com/compute/docs/overview) · [Instances](https://docs.cloud.google.com/compute/docs/instances) · [Regions and zones](https://docs.cloud.google.com/compute/docs/regions-zones) · [VM lifecycle](https://docs.cloud.google.com/compute/docs/instances/instance-lifecycle)

---

## 2. Launching and configuring VM instances

### Steps in the Console

1. Open Compute Engine, then VM instances, then Create instance.
2. Name the VM and pick a region and zone.
3. Choose a machine family and machine type (or a custom machine type).
4. Set the boot disk: operating system image, size and disk type.
5. Set identity and API access (which service account the VM uses).
6. Under Firewall, tick Allow HTTP or HTTPS traffic if it is a web server.
7. Open Advanced options for networking, security (Shielded VM), startup script and metadata, and the provisioning model (standard or Spot).
8. Click Create.

### Same thing with the command line

```bash
gcloud compute instances create my-vm \
  --zone=asia-south1-a \
  --machine-type=e2-medium \
  --image-family=debian-12 \
  --image-project=debian-cloud
```

### Things to remember

- The zone is fixed when you create the VM. To move it you must recreate it or use the move steps in the docs.
- To change the machine type, stop the VM first, edit it, then start it again.
- Labels and network tags help you organize VMs and apply firewall rules.
- Instance templates save a VM's settings so you can create many identical VMs or a MIG.
- Host maintenance policy: Migrate (live migration) or Terminate; Automatic restart brings the VM back after a crash.

**Official docs:** [Create and start a VM](https://docs.cloud.google.com/compute/docs/instances/create-start-instance) · [Quickstart: Linux VM](https://docs.cloud.google.com/compute/docs/create-linux-vm-instance) · [Custom machine types](https://docs.cloud.google.com/compute/docs/instances/creating-instance-with-custom-machine-type) · [Edit machine type](https://docs.cloud.google.com/compute/docs/instances/changing-machine-type-of-stopped-instance) · [Instance templates](https://docs.cloud.google.com/compute/docs/instance-templates)

---

## 3. Custom images, snapshots, and disks

Easy rule: an image is a template to create new VMs, a snapshot is a backup of a disk, and a disk is the storage itself.

| Item | What it is | Use it to |
| --- | --- | --- |
| Disk | Block storage attached to a VM (boot disk or extra data disk) | Store the OS and data |
| Public image | Ready-made OS from Google (Debian, Ubuntu, Windows and more) | Start a new VM quickly |
| Custom image | Your own boot-disk template with your software and settings already installed | Launch many identical VMs |
| Snapshot | A saved copy of a disk at one moment; later snapshots store only the changes | Back up and restore a disk |
| Machine image | Backup of a whole VM: its settings, metadata and all its disks | Clone or restore a full VM |

### Making a custom image

1. Create a VM, install your software and configure it.
2. Stop the VM (best for a clean copy).
3. Create an image from its boot disk. You can also build one from a snapshot, another image or a file in Cloud Storage.
4. Create new VMs from that image. Put images in an image family so new VMs always pick the latest version.

### Snapshots

- Take them by hand or on a snapshot schedule (for example daily, keep 7 days).
- Restore by creating a new disk from the snapshot, then attach it to a VM.
- Instant snapshots are very fast, in-place backups kept in the same location as the disk.

### Working with disks

- A new data disk must be formatted and mounted inside the VM before use.
- Disks can be resized upward while attached; they cannot be shrunk.
- Boot disks are deleted with the VM by default, so check the auto-delete setting for important data.

**Official docs:** [About OS images](https://docs.cloud.google.com/compute/docs/images) · [Create custom images](https://docs.cloud.google.com/compute/docs/images/create-custom) · [Create a VM from a custom image](https://docs.cloud.google.com/compute/docs/instances/create-vm-from-custom-image) · [Disk snapshots](https://docs.cloud.google.com/compute/docs/disks/snapshots) · [Snapshot schedules](https://docs.cloud.google.com/compute/docs/disks/about-snapshot-schedules) · [Machine images](https://docs.cloud.google.com/compute/docs/machine-images) · [Add a Persistent Disk](https://docs.cloud.google.com/compute/docs/disks/add-persistent-disk) · [Format and mount a disk on Linux](https://docs.cloud.google.com/compute/docs/disks/format-mount-disk-linux)

---

## 4. SSH and startup scripts

### Ways to connect to a Linux VM

| Method | How | Good for |
| --- | --- | --- |
| SSH-in-browser | Click SSH next to the VM in the Console | Quick access, no setup |
| gcloud | `gcloud compute ssh my-vm --zone=asia-south1-a` | Command line; creates and uploads keys for you |
| Standard SSH client | Add your public key, then `ssh user@EXTERNAL_IP` | Your own tools and automation |
| IAP tunnel | `gcloud compute ssh my-vm --tunnel-through-iap` | VMs with no external IP |

### Who is allowed in

- **OS Login (recommended):** access is managed with IAM roles, so you grant or remove a person's access in one place.
- **Metadata SSH keys:** public keys are stored in project or VM metadata. Simple, but harder to control at scale.
- The firewall must allow TCP port 22 (the default network has a rule for this).

### Startup scripts

A startup script is a script that runs automatically every time the VM boots. It is set as metadata, so you can install software without logging in.

```bash
#!/bin/bash
apt-get update
apt-get install -y nginx
echo "Hello from $(hostname)" > /var/www/html/index.html
```

- Add it under Advanced options, Management, Automation in the Console, or with `--metadata-from-file=startup-script=startup.sh`.
- Use the key `startup-script-url` to load a script stored in Cloud Storage.
- It runs as root on each boot, so make it safe to run more than once.
- To debug on Linux: `sudo journalctl -u google-startup-scripts.service`.
- A shutdown script (key `shutdown-script`) runs when the VM stops, but only for a short time.

**Official docs:** [About SSH connections](https://docs.cloud.google.com/compute/docs/instances/ssh) · [Connect to VMs](https://docs.cloud.google.com/compute/docs/connect/standard-ssh) · [About OS Login](https://docs.cloud.google.com/compute/docs/oslogin) · [Add SSH keys to VMs](https://docs.cloud.google.com/compute/docs/connect/add-ssh-keys) · [Connect using IAP](https://docs.cloud.google.com/compute/docs/connect/ssh-using-iap) · [Startup scripts overview](https://docs.cloud.google.com/compute/docs/instances/startup-scripts) · [Linux startup scripts](https://docs.cloud.google.com/compute/docs/instances/startup-scripts/linux) · [Shutdown scripts](https://docs.cloud.google.com/compute/docs/shutdownscript) · [About VM metadata](https://docs.cloud.google.com/compute/docs/metadata/overview)

---

## 5. Google Cloud Marketplace

Google Cloud Marketplace is an app store inside Google Cloud. It lists ready-to-run software from Google and partner companies, so you can launch it without installing everything by hand.

- **What you find:** web stacks (LAMP, WordPress), databases, developer tools, security software, VM images and SaaS products.
- **How to use it:** open Marketplace in the Console, search for a product, click Launch, review the VM size and settings, then Deploy. Many products are deployed as one or more Compute Engine VMs set up for you.
- **Cost:** you pay for the underlying resources (VMs, disks, network). Some products add a software fee, billed through your Google Cloud account, and some let you bring your own license.
- **Why it helps:** faster setup, tested configurations, and one bill.
- **Good habit:** read the product page for the vendor, version and pricing before you deploy.

**Official docs:** [Google Cloud Marketplace](https://cloud.google.com/marketplace) · [Marketplace documentation](https://cloud.google.com/marketplace/docs)

> I could not open the Marketplace docs pages while preparing these notes, so check them for current details.

---

## 6. Volume (disk) types in GCP

The official disk page groups block storage into Hyperdisk, Persistent Disk and Local SSD. Persistent Disk and Hyperdisk are network storage, so data survives if the VM stops. Local SSD is temporary.

| Family | Type | Plain-words use |
| --- | --- | --- |
| Hyperdisk | Balanced | General purpose; you set size, IOPS and throughput separately |
| Hyperdisk | Balanced High Availability | Same idea, but data is kept in two zones for failover |
| Hyperdisk | Extreme | Highest IOPS, for large databases |
| Hyperdisk | ML | Fast read-only loading of AI model data by many VMs |
| Hyperdisk | Throughput | High data speed for big sequential reads and writes, such as analytics |
| Persistent Disk | Standard (HDD) | Cheapest, slower; large sequential data and backups |
| Persistent Disk | Balanced (SSD) | Good price and speed for most apps |
| Persistent Disk | SSD | Faster SSD for databases and busy apps |
| Persistent Disk | Extreme | Very high IOPS Persistent Disk |
| Local SSD | Attached to the host | Fastest, cache and scratch data; data is lost when the VM stops |

### Tips

- Hyperdisk lets you change performance without changing size; Persistent Disk performance grows with size.
- Many newer machine series work only with Hyperdisk, so check which disks your machine series supports.
- Regional disks and replication copy data across zones or regions for high availability.
- All disks are encrypted by default.

**Official docs:** [Choose a disk type](https://docs.cloud.google.com/compute/docs/disks) · [Hyperdisk overview](https://docs.cloud.google.com/compute/docs/disks/hyperdisks) · [About Persistent Disk](https://docs.cloud.google.com/compute/docs/disks/persistent-disks) · [About Local SSD](https://docs.cloud.google.com/compute/docs/disks/local-ssd) · [Hyperdisk Balanced](https://docs.cloud.google.com/compute/docs/disks/hd-types/hyperdisk-balanced) · [Hyperdisk Extreme](https://docs.cloud.google.com/compute/docs/disks/hd-types/hyperdisk-extreme) · [Hyperdisk ML](https://docs.cloud.google.com/compute/docs/disks/hd-types/hyperdisk-ml) · [Hyperdisk Throughput](https://docs.cloud.google.com/compute/docs/disks/hd-types/hyperdisk-throughput)

---

## 7. Instance (machine) family types

A machine family is a group of VM series built for a similar kind of work. A name like `n2-standard-4` reads as: series N2, memory shape standard, 4 vCPUs (16 GB memory).

| Family | Series (from the official guide) | Best for |
| --- | --- | --- |
| General-purpose | E2, N1, N2, N2D, N4, N4A, N4D, C3, C3D, C4, C4A, C4D, Tau T2D, Tau T2A | Web and app servers, microservices, databases, dev and test |
| Compute-optimized | H4D, H3, C2, C2D | HPC and CPU-heavy work |
| Memory-optimized | X5, X4, M4, M4N, M3, M2, M1 | In-memory databases, large analytics (up to 12 TB memory) |
| Storage-optimized | Z3, Z4D, Z4M | Large local storage: SQL, NoSQL, search, data warehouses |
| Network-optimized | C4N, M4N | High network bandwidth and packet processing |
| Accelerator-optimized (GPU) | A4X Max, A4X, A4, A3, A2, G4, G2 | AI and ML training and inference, graphics, HPC |

### Inside the general-purpose family

- **E2:** lowest cost for everyday work; also has shared-core sizes (`e2-micro`, `e2-small`, `e2-medium`) for small apps.
- **N-series (N1, N2, N2D, N4):** balanced price and performance.
- **C-series (C3, C4 and others):** consistently high performance for busy servers and databases.
- **Tau (T2D, T2A):** best price per core for scale-out web and container work.

### Memory shapes

- `highcpu`: about 2 GB per vCPU.
- `standard`: about 4 GB per vCPU.
- `highmem`: about 8 GB per vCPU.
- Bigger ratios (`megamem`, `ultramem`, `hypermem`) appear in memory-optimized series.

### Other things to know

- Predefined types have fixed vCPU and memory. Custom machine types (N and E series) let you pick your own, at about a 5% higher price.
- Higher numbers (N2 to N4, C3 to C4) mean a newer generation.
- Types with `lssd` in the name come with Local SSD or Titanium SSD attached. Types with `-metal` are bare metal servers with no hypervisor.
- CPU makers: Intel, AMD, and Arm-based (for example N4A, C4A, T2A).

**Official docs:** [Machine families guide](https://docs.cloud.google.com/compute/docs/machine-resource) · [General-purpose](https://docs.cloud.google.com/compute/docs/general-purpose-machines) · [Compute-optimized](https://docs.cloud.google.com/compute/docs/compute-optimized-machines) · [Memory-optimized](https://docs.cloud.google.com/compute/docs/memory-optimized-machines) · [Storage-optimized](https://docs.cloud.google.com/compute/docs/storage-optimized-machines) · [Network-optimized](https://docs.cloud.google.com/compute/docs/network-optimized-machines) · [GPU machines](https://docs.cloud.google.com/compute/docs/accelerator-optimized-machines) · [CPU platforms](https://docs.cloud.google.com/compute/docs/cpu-platforms)

---

## 8. Purchase (provisioning) types

The official docs say each provisioning model decides a VM's availability, lifespan and price. Discounts and reservations are separate options you can add on top.

| Option | Plain-words meaning | Best for |
| --- | --- | --- |
| Standard (on-demand) | Normal VM, runs until you stop it, billed per second | Production apps and anything that must not be interrupted |
| Spot VMs | Spare capacity at a much lower price; Google can stop the VM when it needs the capacity back | Batch jobs, testing, fault-tolerant work |
| Flex-start VMs | You request capacity and the VM starts when it is available, for a limited run time | Short batch and AI jobs that can wait |
| Reservation-bound VMs | VMs that use capacity you reserved earlier | Guaranteed capacity for critical or planned launches |
| Preemptible VMs | Older low-cost model that runs up to 24 hours; Spot VMs replace it | Legacy only |

### Ways to save money or secure capacity

- **Committed use discounts (CUDs):** promise a set amount of resources for a fixed term (1 or 3 years) and pay less. Best for steady, predictable usage.
- **Sustained use discounts:** applied automatically when certain VM types run for a large part of the month. No commitment needed.
- **On-demand reservations:** hold capacity in a zone so it is there when you need it. You pay while it is reserved, and you can combine it with a CUD.
- **Future reservations:** ask in advance for capacity on a date.

### Quick choice guide

- Must stay up: Standard, plus CUDs if usage is steady.
- Can restart safely: Spot.
- Big event or launch: add a reservation.

**Official docs:** [About VM provisioning models](https://docs.cloud.google.com/compute/docs/instances/provisioning-models) · [Spot VMs](https://docs.cloud.google.com/compute/docs/instances/spot) · [Flex-start VMs](https://docs.cloud.google.com/compute/docs/instances/about-flex-start-vms) · [Preemptible VMs](https://docs.cloud.google.com/compute/docs/instances/preemptible) · [Reservations](https://docs.cloud.google.com/compute/docs/instances/reservations-overview) · [Committed use discounts](https://docs.cloud.google.com/compute/docs/instances/committed-use-discounts-overview) · [Sustained use discounts](https://docs.cloud.google.com/compute/docs/sustained-use-discounts)

Prices and discount percentages change, so check the [Compute Engine pricing page](https://cloud.google.com/compute/all-pricing) before you plan costs.

---

## Sources

The machine family table is taken from the official guide that I opened on 24 Oct 2026. The disk, provisioning and Marketplace pages loaded only in part or not at all, so those sections follow the page titles and navigation shown there plus general knowledge of the service. Check the linked pages for exact current limits and prices.

- [Machine families resource and comparison guide](https://docs.cloud.google.com/compute/docs/machine-resource)
- [Compute Engine documentation home](https://docs.cloud.google.com/compute/docs)
- [Choose a disk type](https://docs.cloud.google.com/compute/docs/disks)
- [About VM provisioning models](https://docs.cloud.google.com/compute/docs/instances/provisioning-models)
- [Google Cloud Marketplace](https://cloud.google.com/marketplace)
