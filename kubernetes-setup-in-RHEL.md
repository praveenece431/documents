# Installing and configuring the Kubernetes cluster with a Master and Worker nodes in RedHat/CentOs

This guide provides step-by-step instructions on how to setup K8s, a popular orchestration tool platform, on Ubuntu.

![K8s Archeicture](https://github.com/praveenece431/documents/blob/main/images/k8s-arc.png)

![Orchestration Tools](https://github.com/praveenece431/documents/blob/main/images/Orchesration-tools.png)

## Kubernetes Setup Guide

### Prerequisites
- RedHat 8.0 or CentOs 8 instance with 4 GB RAM - Master Node - (with ports open to all traffic)
- RedHat 8.0 or CentOs 8 with at least 2 GB RAM - Worker Node - (with ports open to all traffic)

### Execute the below commands in both Master and Worker nodes till step 9.

#### Step 1 - Create the configuration file for containerd:
Become root
```bash
sudo -i
```
```bash
cat <<EOF | sudo tee /etc/modules-load.d/containerd.conf
overlay
br_netfilter
EOF

# Load the modules and open ports [6443 10250] at the firewall:
sudo modprobe overlay
sudo modprobe br_netfilter
sudo firewall-cmd --add-port=6443/tcp --permanent
sudo firewall-cmd --add-port=10250/tcp --permanent
sudo firewall-cmd --reload
```
#### Step 2 - Set the system configurations for Kubernetes networking:
```bash
cat <<EOF | sudo tee /etc/sysctl.d/99-kubernetes-cri.conf
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1
net.bridge.bridge-nf-call-ip6tables = 1
EOF

#Apply the new settings without reboot:
sudo sysctl --system
```
#### Step 3 - Add the stable Docker Community Edition repository to yum:
```bash
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo

```
#### Step 4 - Install containerd:
```bash
sudo yum install -y containerd.io --allowerasing
#Create the default configuration folder for containerd:
sudo mkdir -p /etc/containerd
```
#### Step 5 - Generate the default containerd configuration, and save it to the newly created default file:
```bash
sudo containerd config default | sudo tee /etc/containerd/config.toml
#Restart containerd to ensure the new configuration file is used:
sudo systemctl restart containerd
#Verify that containerd is running:
sudo systemctl status containerd
```
#### Step 6 - Disable swap:
```bash
sudo swapoff -a
```
#### Step 7 - Set SELinux in permissive mode (effectively disabling it):
```bash
sudo setenforce 0
sudo sed -i 's/^SELINUX=enforcing$/SELINUX=permissive/' /etc/selinux/config
```
#### Step 8 - Add the Kubernetes repository (this overwrites any existing configuration in /etc/yum.repos.d/kubernetes.repo):
```bash
cat <<EOF | sudo tee /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://pkgs.k8s.io/core:/stable:/v1.29/rpm/
enabled=1
gpgcheck=1
gpgkey=https://pkgs.k8s.io/core:/stable:/v1.29/rpm/repodata/repomd.xml.key
exclude=kubelet kubeadm kubectl cri-tools kubernetes-cni
EOF
```

#### Step 9 - Install kubelet, kubeadm and kubectl:
```bash
sudo yum install -y kubelet kubeadm kubectl --disableexcludes=kubernetes
#Enable the kubelet service before running kubeadm:
sudo systemctl enable --now kubelet
```
#### Step 10 - Initialize the cluster using the IP range for Flannel Only in master/control-plane:
```bash
kubeadm init --pod-network-cidr=10.244.0.0/16
```
#### Step 11 - Copy the kubeadmn join command that is in the output. We will need this later.

#### Step 12 - Copy and paste this as a regular user. Not with the root user.
```bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```
### Join the worker nodes to the control plane (master)

#### Step 13
#### Execute the below command to print the join command. It will come by default when you initilize the cluster successfully.
```bash
kubeadm token create --print-join-command
Eg:
#sudo kubeadm join 10.0.0.4:6443 --token q093mt.3xjjh9kf9sbztx3o \
#        --discovery-token-ca-cert-hash sha256:862cbf51f0824af210702502514a156d992fa87762f354a004a9bbbc06fed3c7
```
#### Verify the cluster is working
```bash
kubectl get nodes
```

#### Step 14
#### The nodes will show you as in a NOT READY stage. For this, you need to install the network plugin. 
#### This plugin will enable the pod to pod communication.
#### Install the Flannel network add-on only in the Master/Control-plane node
```bash
kubectl apply -f https://github.com/flannel-io/flannel/releases/latest/download/kube-flannel.yml
```

### Verify the cluster again if it is working
```bash
kubectl get pods --all-namespaces
kubectl get nodes
```
### Here you should see all the nodes are in READY state.