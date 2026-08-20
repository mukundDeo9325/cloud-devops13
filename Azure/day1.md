

## Get to know about Azure 
- What is Azure?
- https://azure.microsoft.com/en-us/resources/cloud-computing-dictionary/what-is-azure
![](./images/azurevsaws.png)


![](./images/cloudlead.png)



## Providing cloud service models
- **Infrastructure as a service (IaaS)** delivers essential IT infrastructure for businesses to flexibly create and manage resources.
- **Platform as a service (PaaS)** offers a cloud platform to develop, run, and manage applications without the need to handle underlying infrastructure.
- **Software as a service (SaaS)** provides seamless access to software applications over the internet, eliminating local installation needs.
- **Artificial intelligence as a service (AIaaS)** provides AI tools and services via the cloud to accelerate innovation.
- **Model as a service (MaaS)** delivers machine learning models as serverless APIs for simplified app deployment.

## Azure Portal walkthrough
![](./images/Azure.png)
![](./images/Azure2.png)
![](./images/Azure3.png)



## Introduction to Azure Cloud Shell 

Azure Cloud Shell is an online-based shell provided by Microsoft Azure that allows users to manage their Azure resources directly from a web browser. It offers a pre-configured environment with popular command-line tools and programming languages, enabling users to execute scripts, manage resources, and automate tasks without the need for local installations.

## Launch VM using Azure Cloud Shell

### Step 1: Open Azure Cloud Shell
1. Go to the [Azure Portal](https://portal.azure.com).
2. Click on the Cloud Shell icon (>) in the top right corner.

### Step 2: Create a Resource Group
```bash
az group create   --name myResourceGroup   --location centralindia
```

### Step 3: Create a Virtual Machine
```bash
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image Ubuntu2204 \
  --size Standard_B2ats_v2 \
  --admin-username azureuser \
  --generate-ssh-keys
```
### Step 4: Verify the VM Creation
```bash
az vm show --resource-group myResourceGroup --name myVM --output table
```

### step 5: Connect to the Virtual Machine
```bash
ssh azureuser@<public-ip-address>
```
### Step 6: Clean Up Resources VM, resource group
```bash
az vm delete --resource-group myResourceGroup --name myVM --yes
az group delete --name myResourceGroup --yes --no-wait
```






