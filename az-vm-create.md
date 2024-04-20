# Create a VM in Azure

This guide will walk you through the process of creating a virtual machine (VM) in Azure using the Azure CLI. Before starting, ensure that you have the Azure CLI installed on your machine. If not, you can download and install it from the [Azure CLI documentation](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli).

## Prerequisites

- Azure CLI installed
- Azure subscription (you can create one if you don't have any)

## Install AZ cli in Mobaxterm
```bash
curl --insecure https://raw.githubusercontent.com/abeckDev/MobaXtermAzureCLI/master/install.sh | bash

## Steps to Create a VM

### 1. Login to Azure

First, you need to log in to Azure using the following command. This command uses the device code flow for authentication. Follow the instructions provided in the terminal to authenticate.

```bash
az login --use-device-code
vm1Name="control-plane"
vm2Name="worker-node"
location="eastus2"
vmSize="Standard_D2s_v3"
image="Canonical:UbuntuServer:18.04-DAILY-LTS:18.04.202306070"
adminUsername="azureuser"
adminPassword="YOUR_PASSWORD"
#az group create --name $resourceGroup --location $location

# Create the first VM
az vm create \
  --resource-group $resourceGroup \
  --name $vm1Name \
  --image $image \
  --admin-username $adminUsername \
  --admin-password $adminPassword \
  --size $vmSize \
  --location $location

# Create the second VM
az vm create \
  --resource-group $resourceGroup \
  --name $vm2Name \
  --image $image \
  --admin-username $adminUsername \
  --admin-password $adminPassword \
  --size $vmSize \
  --location $location

