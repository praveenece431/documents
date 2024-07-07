# Installing Docker on Ubuntu and CentOS/RedHat

This guide provides step-by-step instructions on how to install Docker, a popular containerization platform, on Ubuntu and CentOS. Docker allows you to package an application with all of its dependencies into a standardized unit for software development.

## Docker Installation on Ubuntu

### Prerequisites
- An Ubuntu system (Recommended version: Ubuntu 20.04 LTS)
- Sudo privileges

### Steps: Update Your System and install with below steps
Update your package index and upgrade the packages to their latest available versions.

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
sleep 3
sudo apt update
echo "deb [signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sleep 3
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io
sudo systemctl start docker
sudo systemctl enable docker
sudo usermod -aG docker <your-username>
docker --version
````

## Docker Installation on CentOs and RedHat

### Prerequisites
- A CentOS or RedHat system (Recommended version: CentOS 7)
- Sudo privileges

### Steps: Update Your System and install with below steps
```bash
sudo yum update -y
````
### Install Required Packages
```bash
sudo yum install yum-utils -y
```
### Set Up the Stable Repository
```bash
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```
### Install Docker Engine
```bash
sudo yum install docker-ce docker-ce-cli containerd.io -y
```
### Start Docker and enable the service in reboot
```bash
sudo systemctl start docker
sudo systemctl enable docker
sudo systemctl status docker
```
### Verify the docker installation 
```bash
docker version
```



