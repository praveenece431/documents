#                          Terraform IAC Tool

### Terraform is an infrastructure as code (IaC) tool developed by HashiCorp. It allows you to define, create, modify, and manage cloud and on-premises resources in a declarative and human-readable way. Here are the key points about Terraform:

#### Definition of Resources:
- With Terraform, you express your desired infrastructure state using configuration files. These files describe the resources you want to create, such as virtual machines, networks, storage, and more.

#### Versioning and Reusability:
- Terraform configuration files are versioned, which means you can track changes over time. You can also reuse and share these configurations across projects.

#### Providers:
- Terraform works with various cloud platforms and services through providers. Providers are plugins that allow Terraform to interact with APIs of different cloud providers. There are thousands of providers available, including AWS, Azure, Google Cloud, Kubernetes, and more.

#### What is Infrastructure as a Code (IaC)?
- Infrastructure as Code (IaC) is a method of managing and provisioning IT infrastructure using code, rather than manual configuration. It allows teams to automate the setup and management of their infrastructure, making it more efficient and consistent. This is particularly useful in the DevOps environment, where teams are constantly updating and deploying software. To know more about Infrastructure as a Code (IaC).

#### Terraform flow:
![Terraform flow diagram](https://github.com/praveenece431/documents/blob/main/images/terraform-apply.png)

![Terraform flow ](https://github.com/praveenece431/documents/blob/main/images/terraform-workflow.png)

### Terraform installation steps:
#### In Ubuntu OS:
```bash
sudo apt-get update && sudo apt-get install -y gnupg software-properties-common

wget -O- https://apt.releases.hashicorp.com/gpg | \
gpg --dearmor | \
sudo tee /usr/share/keyrings/hashicorp-archive-keyring.gpg > /dev/null
# Verify the key's fingerprint.
gpg --no-default-keyring \
--keyring /usr/share/keyrings/hashicorp-archive-keyring.gpg \
--fingerprint

echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] \
https://apt.releases.hashicorp.com $(lsb_release -cs) main" | \
sudo tee /etc/apt/sources.list.d/hashicorp.list

sudo apt update
sudo apt-get install terraform
# Verify the installation:
terraform -help

# Enable tab completion in bash
touch ~/.bashrc
# Install the autocomplete package.
terraform -install-autocomplete
```
#### In CentOS/RHEL OS:
```bash
sudo yum install -y yum-utils
sudo yum-config-manager --add-repo https://rpm.releases.hashicorp.com/RHEL/hashicorp.repo
sudo yum -y install terraform
# Verify the installation:
terraform -help
# Enable tab completion in bash
touch ~/.bashrc
# Install the autocomplete package.
terraform -install-autocomplete
```

#### In Amazon Linux OS:
```bash
sudo yum install -y yum-utils
sudo yum-config-manager --add-repo https://rpm.releases.hashicorp.com/AmazonLinux/hashicorp.repo
sudo yum -y install terraform
# Verify the installation:
terraform -help
# Enable tab completion in bash
touch ~/.bashrc
# Install the autocomplete package.
terraform -install-autocomplete
```
