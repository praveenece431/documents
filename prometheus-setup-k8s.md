# Prometheus and Grafana setup with HELM in Kubernetes versions higher than 1.24.x

## Prerequisite:
#### You should have a running Kubernetes cluster with a Master and Worker node

#### Install Helm 
```bash
curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
```
#### Create Prometheus namespace
```bash
kubectl create namespace prometheus
```
#### Add Prometheus Helm repo.
```bash
helm repo add stable https://charts.helm.sh/stable
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
```

#### Install kube-Prometheus-stack. Below is the command to install kube-prometheus-stack. The helm repo kube-stack-Prometheus (formerly Prometheus-operator) comes with a Grafana deployment embedded.
```bash
helm install stable prometheus-community/kube-prometheus-stack -n prometheus
```

#### Let’s check if the Prometheus and Grafana pods are running or not.
```bash
kubectl get pods -n prometheus
kubectl get svc -n prometheus
```

#### Edit Prometheus Service to Loadbalancer from ClusterIP.
```bash
kubectl edit svc stable-kube-prometheus-sta-prometheus -n prometheus
# Eg: type: LoadBalancer
```

#### Edit Grafana service similarly as above.
```bash
kubectl edit svc stable-grafana -n prometheus
```
#### Verify if the service is changed to LoadBalancer and also get the Load BalancerPorts.
```bash
kubectl get svc -n prometheus
```

#### Access Grafana, Prometheus UI in the browser.
```bash
#<k8s-master-public-ip:external-ip>
```
#### Login to Grafana dashboard with below default credentials:
```bash
#UserName: admin
#Password: prom-operator
```

#### Create a Dashboard in Grafana. In Grafana, we can create various kinds of dashboards as per our needs.
```bash
# Click the ‘+’ button on the left panel and select ‘Import’.
# Enter the 15661 dashboard id under Grafana.com Dashboard.
# Click ‘Load’.
# Select ‘Prometheus’ as the endpoint under the Prometheus data sources drop-down.
# Click ‘Import’.
# This will show the monitoring dashboard for all cluster nodes
```

#### How to Create Kubernetes Cluster Monitoring Dashboard?
```bash
# Click the ‘+’ button on the left panel and select ‘Import’.
# Enter 3119 dashboard ID under Grafana.com Dashboard.
# Click ‘Load’.
# Select ‘Prometheus’ as the endpoint under the Prometheus data sources drop-down.
# Click ‘Import’.
# This will show the monitoring dashboard for all cluster nodes
```

#### Create a POD Monitoring Dashboard.
```bash
# Click the ‘+’ button on the left panel and select ‘Import’.
# Enter 6417 dashboard ID under Grafana.com Dashboard.
# Click ‘Load’.
# Select ‘Prometheus’ as the endpoint under the Prometheus data sources drop-down.
# Click ‘Import’.
```


