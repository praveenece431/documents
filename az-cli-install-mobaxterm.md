# Install AZ cli in Mobaxterm linux local terminal

### To install the Azure CLI (AZ CLI) on Mobaxterm, you'll need to follow a series of steps, primarily because Mobaxterm, an enhanced terminal for Windows with an X11 server, a tabbed SSH client, and several other network tools for remote computing, does not directly support Azure CLI as a native application. However, you can utilize the Linux-like environment provided by Mobaxterm's Cygwin to install and run the Azure CLI.
### Here’s a step-by-step guide to install the Azure CLI in Mobaxterm:
#### Step 1 Update Packages
```bash
apt-get update
```

#### Step 2 Install Python and Pip
```bash
apt-get install python3-pip
```

#### Step 3 Install Azure cli
```bash
pip3 install azure-cli
```

#### Step 4 Verify installation
```bash
az --version
```

### If you get any installation errors, or if installation failed, try the below steps:

#### Upgrade PIP
pip3 install --upgrade pip
#OR
python3 -m pip install --upgrade pip
```
#### Clear PIP cache
```bash
pip3 cache purge
```
#### Try installing AZ CLI again
```bash
pip3 install azure-cli
```
