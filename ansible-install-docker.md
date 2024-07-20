# Installing docker in multiple target Ubuntu VMs 

### Set Up Ansible Control Node
```bash
sudo apt update
sudo apt install ansible -y
```

### Configure Passwordless authentication from Ansible Control Node to all the inventory hosts.
```bash
ssh-keygen -t rsa
ssh-copy-id < target host >
```
### Configure Azure Credentials
 - Install Azure CLI:
 ```bash
 curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
 ```
 ```bash
 az login --use-device-code
 ```
 - Create a Service Principal (If you dont have Servie Principle Account):
 ```bash
 az ad sp create-for-rbac --name "AnsibleServicePrincipal" --role Contributor --scopes /subscriptions/abcd-xxxx
 ```

 - If you have App ID, Client Secret and Tenant ID, then authenticate with the below command:
 ```bash
 az login --service-principal -u 20c600d7-9a94-4ebb-98b8-xxxxx -p Bnh8Q~nvPP1VZ9bwomSO5vpTlwYW5jxxxxxx --tenant 84f1e4ea-8554-43e1-8709-f0bxxxxxxx
 ```

### Create the below inventory file, eg docker-inventory.ini:
```bash
[Ubuntu_VMs]
jenkins ansible_host=52.226.164.36 ansible_user=devops-user ansible_ssh_common_args='-o StrictHostKeyChecking=no'
sonarqube ansible_host=172.190.139.246 ansible_user=devops-user ansible_ssh_common_args='-o StrictHostKeyChecking=no'
nexus ansible_host=104.41.148.131 ansible_user=devops-user ansible_ssh_common_args='-o StrictHostKeyChecking=no'
```
### Create the below playbook and save in docker-playbook.yml:
```bash
---
- name: Install Docker on Ubuntu 22.04 VMs
  hosts: Ubuntu_VMs
  become: yes
  tasks:
    - name: Update and upgrade apt packages
      apt:
        update_cache: yes
        upgrade: dist

    - name: Install required dependencies
      apt:
        name:
          - apt-transport-https
          - ca-certificates
          - curl
          - software-properties-common
        state: present

    - name: Add Docker GPG key
      apt_key:
        url: https://download.docker.com/linux/ubuntu/gpg
        state: present

    - name: Add Docker repository
      apt_repository:
        repo: deb [arch=amd64] https://download.docker.com/linux/ubuntu focal stable
        state: present

    - name: Update apt packages after adding Docker repo
      apt:
        update_cache: yes

    - name: Install Docker
      apt:
        name: docker-ce
        state: present

    - name: Ensure Docker service is started and enabled
      systemd:
        name: docker
        state: started
        enabled: yes
```

### Execute the playbook as:
```bash
ansible-playbook -i docker-inventory.ini docker-playbook.yml
```