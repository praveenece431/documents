# Installing and configuring the Kubernetes cluster with a Master and Worker nodes

This guide provides step-by-step instructions on how to setup K8s, a popular orchestration tool platform, on Ubuntu.

![K8s Archeicture](https://github.com/praveenece431/documents/blob/main/images/k8s-arc.png)

![Orchestration Tools](https://github.com/praveenece431/documents/blob/main/images/Orchesration-tools.png)

## Kubernetes Setup Guide

### Prerequisites
- Ubuntu instance with 4 GB RAM - Master Node - (with ports open to all traffic)
- Ubuntu instance with at least 2 GB RAM - Worker Node - (with ports open to all traffic)
- Disable the firewall if you are running VM's on Cloud
  ```bash
  sudo ufw disable
  ```
 - ### https://www.linkedin.com/pulse/kubernetes-setup-using-kubeadm-aws-ec2-ubuntu-servers-cedric-wanji/

### Execute the below commands in both Master and Worker nodes till step 8.

#### Step 1
```bash

apt-get update
apt-get install -y apt-transport-https ca-certificates curl jq

cat <<EOF > /etc/modules-load.d/k8s.conf
overlay
br_netfilter
EOF
```
#### Step 2
```bash
modprobe overlay
modprobe br_netfilter
```
#### Step 3
```bash
# sysctl params required by setup, params persist across reboots
cat <<EOF > /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-iptables  = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward                 = 1
EOF

# Apply sysctl params without reboot
sysctl --system

```
#### Step 4
#### Install containerd
```bash
apt-get install -y containerd
mkdir -p /etc/containerd
containerd config default > /etc/containerd/config.toml
sed -i 's/SystemdCgroup = false/SystemdCgroup = true/' /etc/containerd/config.toml
systemctl restart containerd
```
#### Step 5
#### Get the latest kubernetes version.
```bash
KUBE_LATEST=$(curl -L -s https://dl.k8s.io/release/stable.txt | awk 'BEGIN { FS="." } { printf "%s.%s", $1, $2 }')
```
#### Step 6
#### Add the repository keys and GPG keys to download the kubetnetes packages.
```bash
mkdir -p /etc/apt/keyrings
curl -fsSL https://pkgs.k8s.io/core:/stable:/${KUBE_LATEST}/deb/Release.key | gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
echo "deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/${KUBE_LATEST}/deb/ /" > /etc/apt/sources.list.d/kubernetes.list
```
#### Step 7
### On all nodes, install kubeadm, kubelet, and kubectl
```bash
apt-get update
apt-get install -y kubelet kubeadm kubectl
apt-mark hold kubelet kubeadm kubectl
```
#### Step 8
#### Set crictl default config in all the nodes.
```bash
crictl config \
        --set runtime-endpoint=unix:///run/containerd/containerd.sock \
        --set image-endpoint=unix:///run/containerd/containerd.sock

#(Optional) Enable the kubelet service before running kubeadm:
sudo systemctl enable --now kubelet
```
#### Step 9 Only on Control Plane
#### On the control plane node only, initialize the cluster and set up kubectl access
```bash
kubeadm init
```

#### Step 10
#### Copy and paste this as a regular user. Not with the root user.
```bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config

sudo cp /etc/kubernetes/admin.conf .
sudo chmod 666 admin.conf
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
#### Install the Calico or weavenet network add-on only in the Master/Control-plane node. Any one network plugin. Not both.
```bash
kubectl --kubeconfig /etc/kubernetes/admin.conf apply -f https://github.com/weaveworks/weave/releases/download/v2.8.1/weave-daemonset-k8s.yaml

#kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.25.0/manifests/calico.yaml
```

### Verify the cluster again if it is working
```bash
kubectl get nodes
kubectl get pods -n kube-system
```
### Here you should see all the nodes are in READY state.

## Install metrics server and Kubernetes Dashboard.

#### Follow the below steps to install dashboard.

```bash
# Install Kubernetes Dashboard
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml
```
#### Patch the Kubernetes Dashboard service to use NodePort.
```bash
kubectl -n kubernetes-dashboard patch svc kubernetes-dashboard -p '{"spec": {"type": "NodePort"}}'

#Get the NodePort assigned to the Kubernetes Dashboard:
kubectl -n kubernetes-dashboard get svc kubernetes-dashboard

```
#### Install Metrics Server.
```bash
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
# Add the following arguments under spec.containers.args:
- --kubelet-insecure-tls
- --kubelet-preferred-address-types=InternalIP,Hostname,ExternalIP
```
####  Create a Service Account and ClusterRoleBinding.
```bash
# Create a Service Account:
kubectl create serviceaccount dashboard-admin-sa -n kubernetes-dashboard
#Create a ClusterRoleBinding:
kubectl create clusterrolebinding dashboard-admin-sa --clusterrole=cluster-admin --serviceaccount=kubernetes-dashboard:dashboard-admin-sa
```
### Create Secret token
```bash
# For later versions greater then 1.24 no longer automatically create secrets for service accounts. Instead, you need to manually create a token for the service account. Here’s how you can do it:
# Create the token.
kubectl create token dashboard-admin-sa -n kubernetes-dashboard

# Retrieve the Token:
TOKEN=$(kubectl create token dashboard-admin-sa -n kubernetes-dashboard)
echo $TOKEN
```
#### Access the Dashboard:
```bash
# Run this from your local machine. You need to setup kubectl and kubeconfig in your local and access to cluster.
kubectl proxy
```
#### Access the dashboard using the following URL:
```bash
http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/

# provide the token above retrieved to access the dashboard.
```






