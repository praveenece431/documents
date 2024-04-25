# Step by step commands to create Kubernetes cluster in AKS (Azure Kubernetes Service).
## AKS is a fuly managed service in Azure

### Pre-Requisites
- Should have a valid Azure subscription
- Should have a vaild access roles to create resources.
- Should have installed azure-cli in your local or VM.

#### Login to Azure subscription
```bash
az login --use-device-code
```
#### Set the azure subscription to the right one
```bash
az account set --subscription "Enter Subsctiption ID"
```
#### Create resource group in the desired region/location.
```bash
az group create --name ResourceGroup --location Location
$Eg: az group create --name learning-aks --location eastus2
```
#### Create AKS cluster
```bash
az aks create \
  --resource-group ResourceGroup \
  --name AKS ClusterName \
  --node-count NodeCount \
  --node-vm-size NodeSize \
  --location Location \
  --service-principal ServicePrincipalId \
  --client-secret ServicePrincipalSecret
  ```
  ```bash
  # Example:
  az aks create \
  --resource-group learning-RG \
  --name learning-aks \
  --node-count 2 \
  --node-vm-size Standard_D2s_v3 \
  --location eastus2 \
  --service-principal 6d1145d9-*****-***** \
  --client-secret yeT8Q~48U-*****casafaf*****
  ```

