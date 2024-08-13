## AZ CLI storage commands 

```bash
#!/bin/bash

# Variables
CLIENT_ID="clientID"
CLIENT_SECRET="Client Secret"
SUBSCRIPTION_ID="Sub ID"
RESOURCE_GROUP="1-34154949-dummy"
STORAGE_ACCOUNT_NAME="devopsbandla$RANDOM"
CONTAINER_NAME="backups"
TENANT_ID="84f1e4ea-8554-dummy"

# Login to Azure
az login --service-principal -u $CLIENT_ID -p $CLIENT_SECRET --tenant $TENANT_ID

# Create a resource group (if it doesn't exist)
#az group create --name $RESOURCE_GROUP --location eastus

# Create a storage account
az storage account create --name $STORAGE_ACCOUNT_NAME --resource-group $RESOURCE_GROUP --location eastus --sku Standard_LRS

# Get the storage account key
ACCOUNT_KEY=$(az storage account keys list --resource-group $RESOURCE_GROUP --account-name $STORAGE_ACCOUNT_NAME --query '[0].value' --output tsv)

# Create a container with public access
az storage container create --name $CONTAINER_NAME --account-name $STORAGE_ACCOUNT_NAME --account-key $ACCOUNT_KEY --public-access blob

echo "Storage account and container created successfully."
```

### Az cli to upload files
```bash
STORAGE_ACCOUNT_NAME="devopsbandla20841"
RESOURCE_GROUP="1-34154949-playground-sandbox"
ACCOUNT_KEY=$(az storage account keys list --resource-group $RESOURCE_GROUP --account-name $STORAGE_ACCOUNT_NAME --query '[0].value' --output tsv)

az storage blob upload \
    --account-name devopsbandla20841 \
    --container-name backups \
    --name jenkins.tar \
    --file jenkins.tar \
    --account-key $ACCOUNT_KEY
```
