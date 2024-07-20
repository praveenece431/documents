
# Working with Ansible, Installation for regular learning

### Set Up Ansible Control Node
```bash
sudo apt update
sudo apt install ansible
```

### Configure Passwordless authentication from Ansible Control Node to all the inventory hosts.
```bash
ssh-keygen -t rsa
ssh-copy-id < target host >
```

### Install the Azure Ansible collection (for azure cloud):
```bash
ansible-galaxy collection install azure.azcollection
```

### Configure Azure Credentials
 - Install Azure CLI:
 ```bash
 curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
 az login --use-device-code
 ```
 - Create a Service Principal:
 ```bash
 az ad sp create-for-rbac --name "AnsibleServicePrincipal" --role Contributor --scopes /subscriptions/abcd-xxxx
 ```
#### This will output credentials in JSON format, including appId, password, and tenant.

### Set Up Inventory File:
#### Eg: I want to configure Jenkins, SonarQube and Nexus. Below is the sample inventory file:

  - Create an inventory file inventory.ini:
  ```bash
  [jenkins]
vm-jenkins ansible_host=<IP_ADDRESS_JENKINS> ansible_user=<USERNAME>

[sonarqube]
vm-sonarqube ansible_host=<IP_ADDRESS_SONARQUBE> ansible_user=<USERNAME>

[nexus]
vm-nexus ansible_host=<IP_ADDRESS_NEXUS> ansible_user=<USERNAME>
  ```
  - Create the below playbook and save as install-tools.yml

```bash  
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
        url: https://pkg.jenkins.io/debian/jenkins.io.key
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

- name: Install SonarQube
  hosts: sonarqube
  become: yes
  tasks:
    - name: Install Java
      apt:
        name: openjdk-11-jdk
        state: present

    - name: Download SonarQube
      get_url:
        url: https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-8.9.0.43852.zip
        dest: /tmp/sonarqube.zip

    - name: Install unzip
      apt:
        name: unzip
        state: present

    - name: Unzip SonarQube
      unarchive:
        src: /tmp/sonarqube.zip
        dest: /opt
        remote_src: yes

    - name: Move SonarQube to proper directory
      command: mv /opt/sonarqube-8.9.0.43852 /opt/sonarqube

    - name: Create SonarQube user
      user:
        name: sonarqube
        system: yes

    - name: Change ownership of SonarQube directory
      file:
        path: /opt/sonarqube
        state: directory
        owner: sonarqube
        group: sonarqube
        recurse: yes

    - name: Create SonarQube service
      copy:
        dest: /etc/systemd/system/sonarqube.service
        content: |
          [Unit]
          Description=SonarQube service
          After=syslog.target network.target

          [Service]
          Type=simple
          User=sonarqube
          Group=sonarqube
          ExecStart=/opt/sonarqube/bin/linux-x86-64/sonar.sh start
          ExecStop=/opt/sonarqube/bin/linux-x86-64/sonar.sh stop
          LimitNOFILE=65536
          Restart=always

          [Install]
          WantedBy=multi-user.target

    - name: Reload systemd daemon
      command: systemctl daemon-reload

    - name: Start and enable SonarQube
      systemd:
        name: sonarqube
        state: started
        enabled: yes

- name: Install Nexus
  hosts: nexus
  become: yes
  tasks:
    - name: Install Java
      apt:
        name: openjdk-11-jdk
        state: present

    - name: Download Nexus
      get_url:
        url: https://download.sonatype.com/nexus/3/latest-unix.tar.gz
        dest: /tmp/nexus.tar.gz

    - name: Extract Nexus
      unarchive:
        src: /tmp/nexus.tar.gz
        dest: /opt
        remote_src: yes

    - name: Move Nexus to proper directory
      command: mv /opt/nexus-* /opt/nexus
      args:
        removes: /opt/nexus-*

    - name: Create Nexus user
      user:
        name: nexus
        system: yes

    - name: Change ownership of Nexus directory
      file:
        path: /opt/nexus
        state: directory
        owner: nexus
        group: nexus
        recurse: yes

    - name: Create Nexus service
      copy:
        dest: /etc/systemd/system/nexus.service
        content: |
          [Unit]
          Description=nexus service
          After=network.target

          [Service]
          Type=forking
          LimitNOFILE=65536
          User=nexus
          Group=nexus
          ExecStart=/opt/nexus/bin/nexus start
          ExecStop=/opt/nexus/bin/nexus stop
          User=nexus
          Restart=on-abort

          [Install]
          WantedBy=multi-user.target

    - name: Reload systemd daemon
      command: systemctl daemon-reload

    - name: Start and enable Nexus
      systemd:
        name: nexus
        state: started
        enabled: yes
  ```

### Execute the plybook as shown below:
```bash
ansible-playbook -i inventory.ini install-tools.yml
```
