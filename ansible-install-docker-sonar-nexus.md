# Installing docker in multiple target Ubuntu VMs, Jenkins, Sonarqube and Nexus 

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
[docker_hosts]
sonarqube_host ansible_host=172.190.139.246 ansible_user=devops-user ansible_ssh_common_args='-o StrictHostKeyChecking=no'
nexus_host ansible_host=104.41.148.131 ansible_user=devops-user ansible_ssh_common_args='-o StrictHostKeyChecking=no'

[jenkins]
jenkins_host ansible_host=52.226.164.36 ansible_user=devops-user ansible_ssh_common_args='-o StrictHostKeyChecking=no'
[sonarqube]
sonarqube_host ansible_host=172.190.139.246 ansible_user=devops-user ansible_ssh_common_args='-o StrictHostKeyChecking=no'
[nexus]
nexus_host ansible_host=104.41.148.131 ansible_user=devops-user ansible_ssh_common_args='-o StrictHostKeyChecking=no'

```
### Create the below playbook and save in tools-playbook.yml:

```bash
---
- name: Install Jenkins
  hosts: jenkins
  become: yes
  tasks:
    - name: Install Java
      apt:
        name: openjdk-11-jdk
        state: present

    - name: Add Jenkins repository key
      apt_key:
        url: https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
        state: present

    - name: Add Jenkins repository
      apt_repository:
        repo: deb http://pkg.jenkins.io/debian-stable binary/
        state: present

    - name: Install Jenkins
      apt:
        name: jenkins
        state: present

    - name: Start and enable Jenkins
      systemd:
        name: jenkins
        state: started
        enabled: yes
        
- name: Deploy Docker Containers
  hosts: docker_hosts
  become: yes
  tasks:
    - name: Ensure Docker is installed
      apt:
        name: docker-ce
        state: present
        update_cache: yes

    - name: Ensure pip3 is installed
      apt:
        name: python3-pip
        state: present

    - name: Install Docker SDK for Python
      pip:
        name: docker
        executable: pip3

    - name: Ensure Docker service is started and enabled
      systemd:
        name: docker
        state: started
        enabled: yes

- name: Deploy SonarQube
  hosts: sonarqube_host
  become: yes
  tasks:
    - name: Pull SonarQube image
      docker_image:
        name: sonarqube
        source: pull

    - name: Run SonarQube container
      docker_container:
        name: sonarqube
        image: sonarqube
        state: started
        restart_policy: always
        ports:
          - "9000:9000"
          - "9092:9092"

- name: Deploy Nexus
  hosts: nexus_host
  become: yes
  tasks:
    - name: Pull Nexus image
      docker_image:
        name: sonatype/nexus3
        source: pull

    - name: Run Nexus container
      docker_container:
        name: nexus
        image: sonatype/nexus3
        state: started
        restart_policy: always
        ports:
          - "8081:8081"

```