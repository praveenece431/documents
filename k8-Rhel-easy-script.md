# Script for K8s easy and quick setup in RHEL/CenOS

#### Copy this script to a file in a script and execute in both Master and Worker nodes.
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

cat <<EOF | sudo tee /etc/sysctl.d/99-kubernetes-cri.conf
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1
net.bridge.bridge-nf-call-ip6tables = 1
EOF

#Apply the new settings without reboot:
sudo sysctl --system

yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
sleep 3
sudo yum install -y containerd.io --allowerasing
sleep 3
#Create the default configuration folder for containerd:
sudo mkdir -p /etc/containerd

sudo containerd config default | sudo tee /etc/containerd/config.toml
#Restart containerd to ensure the new configuration file is used:
sudo systemctl restart containerd
#Verify that containerd is running:
sudo systemctl enable containerd
sudo swapoff -a
sudo setenforce 0
sudo sed -i 's/^SELINUX=enforcing$/SELINUX=permissive/' /etc/selinux/config

cat <<EOF | sudo tee /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://pkgs.k8s.io/core:/stable:/v1.29/rpm/
enabled=1
gpgcheck=1
gpgkey=https://pkgs.k8s.io/core:/stable:/v1.29/rpm/repodata/repomd.xml.key
exclude=kubelet kubeadm kubectl cri-tools kubernetes-cni
EOF

sudo yum install -y kubelet kubeadm kubectl --disableexcludes=kubernetes
#Enable the kubelet service before running kubeadm:
sudo systemctl enable --now kubelet
```

#### Execute the below command only in the Master
```bash
kubeadm init --pod-network-cidr=10.244.0.0/16
```

#### Install the Flannel network add-on only in the Master/Control-plane node
```bash
kubectl apply -f https://github.com/flannel-io/flannel/releases/latest/download/kube-flannel.yml
```