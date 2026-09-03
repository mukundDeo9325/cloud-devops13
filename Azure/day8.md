## 1. Creating and Managing Virtual Networks (VNet)

### What is a VNet?
<cite index="35-1">An Azure Virtual Network (VNet) is a representation of your own network in the cloud — a logical isolation of the Azure cloud dedicated to your subscription.</cite> It's the **fundamental building block** for any private network in Azure — VMs, App Services, and many other resources connect through it.

### Core Capabilities
- <cite index="35-1">Each VNet has its own CIDR block and can be linked to other VNets and on-premises networks if the CIDR blocks don't overlap</cite>
- <cite index="35-1">You control DNS server settings for VNets, and can segment the VNet into subnets</cite>
- <cite index="38-1">VNets enable secure network connections between virtual machines, the internet, and other Azure services such as Azure SQL Database</cite>
- <cite index="40-1">Azure NAT Gateway simplifies outbound-only internet connectivity for a VNet — fully managed, highly resilient, and doesn't require a load balancer or public IPs directly on VMs</cite>

### Creating a VNet

**Portal:** Search "Virtual networks" → Create → set Resource Group, Name, Region, Address space, Subnets → Review + Create

**CLI:**
```bash
az network vnet create \
  --resource-group rg-network-day8 \
  --name vnet-demo \
  --address-prefix 10.0.0.0/16 \
  --subnet-name frontend-subnet \
  --subnet-prefix 10.0.1.0/24
```

**PowerShell:**
```powershell
New-AzVirtualNetwork `
  -ResourceGroupName "rg-network-day8" `
  -Location "EastUS" `
  -Name "vnet-demo" `
  -AddressPrefix "10.0.0.0/16"
```
<cite index="38-1">This creates a network that can then be segmented into subnets — for example, one for front-end services and another for back-end services.</cite>

### Managing an Existing VNet
- <cite index="39-1">The Overview page shows address space and DNS servers; the Diagram view shows a visual representation of all connected devices</cite>
- List/view via CLI: `az network vnet list` / `az network vnet show`
- List/view via PowerShell: `Get-AzVirtualNetwork`

📘 **Official Docs:** [Quickstart: Create an Azure Virtual Network – Microsoft Learn](https://learn.microsoft.com/en-us/azure/virtual-network/quickstart-create-virtual-network)


## 2. IP Addressing, Subnetting Basics

### CIDR Notation Recap
Azure address spaces use **CIDR notation** (e.g., `10.0.0.0/16`) — the number after the slash indicates how many bits are fixed for the network portion; the rest is available for hosts/subnets.

| CIDR | # of IP Addresses | Common Use |
|---|---|---|
| /16 | 65,536 | Typical VNet-level address space |
| /24 | 256 | Typical subnet size |
| /27 | 32 | Small subnet (e.g., app tier) |
| /29 | 8 | <cite index="39-1">Smallest subnet range Azure allows</cite> |

### Reserved Addresses — Critical Teaching Point
<cite index="39-1">Azure reserves the first and last address in each subnet for protocol conformance, and three more addresses for Azure service usage — meaning a /29 subnet, despite having 8 addresses total, only has 3 usable addresses.</cite>

**Always plan for 5 reserved addresses per subnet:**
```
10.0.1.0/24 example:
10.0.1.0   → Network address (reserved)
10.0.1.1   → Reserved by Azure (default gateway)
10.0.1.2   → Reserved by Azure (DNS mapping)
10.0.1.3   → Reserved by Azure (DNS mapping)
10.0.1.255 → Broadcast address (reserved)
─────────────────────────────
10.0.1.4 - 10.0.1.254 → Usable (251 addresses)
```

### Subnet Planning Best Practices
- <cite index="41-1">Plan your IP address space by allocating a /16 CIDR pool that avoids overlap with your on-premises address ranges</cite> — this is essential before any hybrid connectivity (VPN/ExpressRoute) is set up
- <cite index="41-1">Reserve subnet space for cross-cloud connectivity components, such as a `GatewaySubnet` for VPN Gateway, so transit infrastructure has room to grow</cite>
- Segment subnets by **tier or function** (web, app, database) — this supports NSG rules and routing policies per tier
- Leave room to grow — don't carve a `/16` into all `/24`s immediately if you might need larger subnets later

### Special Reserved Subnet Names
| Subnet Name | Purpose |
|---|---|
| `GatewaySubnet` | Required for VPN Gateway / ExpressRoute Gateway |
| `AzureBastionSubnet` | Required for Azure Bastion (min. /26 — from Day 5) |
| `AzureFirewallSubnet` | Required for Azure Firewall (from Day 5) |

📘 **Official Docs:** [Azure virtual networks and subnets – Microsoft Learn](https://learn.microsoft.com/en-us/azure/networking/design-guide/vnets-subnets)
