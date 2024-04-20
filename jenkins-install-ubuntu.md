# Installing Jenkins on Ubuntu

This README provides detailed instructions on how to install Jenkins on Ubuntu. Jenkins is an open-source automation server that enables developers around the world to reliably build, test, and deploy their software.

## Prerequisites

- A machine with Ubuntu installed
- Sudo privileges on the machine

## Steps to Install Jenkins

### 1. Update Your System

Start by updating the package list and upgrading the existing packages to the latest version.

```bash
sudo apt update -y && sudo apt upgrade -y
sudo apt install openjdk-17-jre -y

curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | sudo tee \
  /usr/share/keyrings/jenkins-keyring.asc > /dev/null
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update -y
sudo apt-get install jenkins -y
sudo systemctl enable jenkins
```
### 2. Open the inbound firewall port in Azure Network settings 

### 3. Access the jenkins from its public IP of the node and port number.
```bash
Eg: http://hostnameIP:8080
