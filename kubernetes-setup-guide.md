# Installing and configuring the Kubernetes cluster with a Master and Worker nodes

This guide provides step-by-step instructions on how to setup K8s, a popular orchestration tool platform, on Ubuntu.

![K8s Archeicture](https://github.com/praveenece431/documents/blob/main/images/k8s-arc.png)

![Orchestration Tools](https://github.com/praveenece431/documents/blob/main/images/Orchesration-tools.png)

## Kubernetes Setup Guide

### Prerequisites
- Ubuntu instance with 4 GB RAM - Master Node - (with ports open to all traffic)
- Ubuntu instance with at least 2 GB RAM - Worker Node - (with ports open to all traffic)

### Execute the below commands in both Master and Worker nodes till step 8.

#### Step 1
```bash
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
overlay
br_netfilter
EOF

sudo modprobe overlay
sudo modprobe br_netfilter
```
#### Step 2
```bash
cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-iptables  = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward                 = 1
EOF

sudo sysctl --system
```
#### Step 3
```bash
sudo apt-get install -y apt-transport-https ca-certificates curl gpg
sudo apt-get update 
```
#### Step 4
#### Add Docker’s official GPG key
```bash
sudo mkdir -m 0755 -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```
#### Step 5
#### Update the apt package index
```bash
sudo apt-get update
```
#### Step 6
#### Install Docker Engine, containerd, and Docker Compose
```bash
sudo apt-get install docker.io -y
sudo usermod -aG docker <your system user>
# Eg: sudo usermod -aG docker azureuser
newgrp docker
sudo chmod 777 /var/run/docker.sock
```
#### Step 7
### On all nodes, install kubeadm, kubelet, and kubectl
```bash
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.29/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.29/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
```
#### Step 8
#### Install the kube componentes kubelet, kudeadm, kubectl and kubernetes-cni on all the nodes
```bash
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl kubernetes-cni
sudo apt-mark hold kubelet kubeadm kubectl

#(Optional) Enable the kubelet service before running kubeadm:
sudo systemctl enable --now kubelet
```
#### Step 9 Only on Control Plane
#### On the control plane node only, initialize the cluster and set up kubectl access
```bash
sudo kubeadm init
```

#### Step 10
#### Copy and paste this as a regular user. Not with the root user.
```bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```
### Join the worker nodes to the control plane (master)

#### Step 11
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

#### Step 12
#### The nodes will show you as in a NOT READY stage. For this, you need to install the network plugin. 
#### This plugin will enable the pod to pod communication.
#### Install the Calico network add-on only in the Master/Control-plane node
```bash
kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.25.0/manifests/calico.yaml
```

### Verify the cluster again if it is working
```bash
kubectl get nodes
kubectl get pods -n kube-system
```
### Here you should see all the nodes are in READY state.