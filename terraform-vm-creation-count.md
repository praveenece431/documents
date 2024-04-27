# Terraform VM's creation with count

### This is a simple use case demp for a beginer 
#### providers.tf
```bash
terraform {
  required_providers {
    azurerm = {
      source  = "hashicorp/azurerm"
      version = "3.101.0"
    }
  }
}

provider "azurerm" {
  features {}
  skip_provider_registration = "true"
}
```

vm.tf
```bash

# ssh.tf
# resource "azurerm_resource_group" "example" {
#   name     = "myResourceGroup"
#   location = "East US"
# }

resource "azurerm_virtual_network" "tf-vnet" {
  name                = "myVnetf-vnett"
  address_space       = ["10.0.0.0/16"]
  location            = "eastus"
  resource_group_name = "1-af1e679b-playground-sandbox"
}

resource "azurerm_subnet" "tf-subnet" {
  name                 = "tf-subnet"
  resource_group_name  = "1-af1e679b-playground-sandbox"
  virtual_network_name = azurerm_virtual_network.tf-vnet.name
  address_prefixes     = ["10.0.1.0/24"]
}

resource "azurerm_public_ip" "tf-pubip" {
  count               = 3
  name                = "tf-pubip-${count.index}"
  location            = "eastus"
  resource_group_name = "1-af1e679b-playground-sandbox"
  allocation_method   = "Static"
}

resource "azurerm_linux_virtual_machine" "tf-vm" {
  count               = 3
  name                            = "tf-vm-${count.index}"
  resource_group_name             = "1-af1e679b-playground-sandbox"
  location                        = "eastus"
  size                            = "Standard_D2s_v3"
  admin_username                  = "azureuser"
  admin_password                  = "AzureAdmin1234"
  disable_password_authentication = "false"
  network_interface_ids           = [azurerm_network_interface.tf-nic[count.index].id]
  #   admin_ssh_key {
  #     username   = "azureuser"
  #     public_key = file("~/.ssh/id_rsa.pub")
  #   }

  os_disk {
    caching              = "ReadWrite"
    storage_account_type = "Standard_LRS"
  }

  source_image_reference {
    publisher = "Canonical"
    offer     = "UbuntuServer"
    sku       = "18.04-DAILY-LTS"
    version   = "18.04.202306070"
  }
}

resource "azurerm_network_interface" "tf-nic" {
  count = 3
  name                = "tf-nic-${count.index}"
  location            = "eastus"
  resource_group_name = "1-af1e679b-playground-sandbox"

  ip_configuration {
    name                          = "tf-NICConfig"
    subnet_id                     = azurerm_subnet.tf-subnet.id
    private_ip_address_allocation = "Dynamic"
    public_ip_address_id          = azurerm_public_ip.tf-pubip[count.index].id
  }
}
```

### After this validate the code syntax and execute the below commands:
```bash
terraform fmt
terraform init
terraform validate
terraform plan
terraform apply
```

