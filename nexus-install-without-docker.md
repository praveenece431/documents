## Nexus install and configure without docker in a Ubuntu VM.

```bash
#!/bin/bash

# Update system packages
sudo apt-get update -y

# Install Java 17
sudo apt-get install openjdk-17-jdk -y

# Create a nexus user
sudo useradd -M -d /opt/nexus -s /bin/bash -r nexus

# Allow nexus user to run all commands without a password
echo "nexus ALL=(ALL) NOPASSWD: ALL" | sudo tee /etc/sudoers.d/nexus

# Create a directory for Nexus
sudo mkdir /opt/nexus

# Download the latest Nexus tar file
cd /opt/nexus
sudo wget https://download.sonatype.com/nexus/3/latest-unix.tar.gz

# Extract the downloaded tar file
sudo tar -zxvf latest-unix.tar.gz --strip-components=1

# Set ownership of the Nexus directory
sudo chown -R nexus:nexus /opt/

# Create a systemd service file for Nexus
sudo tee /etc/systemd/system/nexus.service > /dev/null <<EOF
[Unit]
Description=Nexus Repository Manager
After=network.target

[Service]
Type=forking
LimitNOFILE=65536
ExecStart=/opt/nexus/bin/nexus start
ExecStop=/opt/nexus/bin/nexus stop
User=nexus
Restart=on-abort

[Install]
WantedBy=multi-user.target
EOF

# Reload systemd to apply the new service
sudo systemctl daemon-reload

# Enable and start the Nexus service
sudo systemctl enable nexus
sudo systemctl start nexus

# Print the status of the Nexus service
sudo systemctl status nexus
```
