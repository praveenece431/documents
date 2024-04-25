# Install and Set Up kubectl on Linux

## Method 1
### Install kubectl binary with curl on Linux 
```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
```
### Install kubectl
```bash
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
```

### Note:
```bash
If you do not have root access on the target system, you can still install kubectl to the ~/.local/bin directory:

chmod +x kubectl
mkdir -p ~/.local/bin
mv ./kubectl ~/.local/bin/kubectl
# and then append (or prepend) ~/.local/bin to $PATH
```

## Method 2
### Install using native package management 
- In Ubuntu OS
  ```bash
      sudo apt-get update
      # apt-transport-https may be a dummy package; if so, you can skip that package
      sudo apt-get install -y apt-transport-https ca-certificates curl
      # If the folder `/etc/apt/keyrings` does not exist, it should be created before the curl command, read the note below.
      # sudo mkdir -p -m 755 /etc/apt/keyrings
      curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.30/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
      sudo chmod 644 /etc/apt/keyrings/kubernetes-apt-keyring.gpg # allow unprivileged APT programs to read this keyring
      # This overwrites any existing configuration in /etc/apt/sources.list.d/kubernetes.list
      echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.30/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
      sudo chmod 644 /etc/apt/sources.list.d/kubernetes.list   # helps tools such as command-not-found to work correctly
      sudo apt-get update
      sudo apt-get install -y kubectl
  ```

