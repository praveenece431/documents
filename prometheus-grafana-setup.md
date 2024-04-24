# Prometheus and Grafana installation steps in a kubernetes cluster

## Pre-Requisite
Kubernetes Cluster

### Why monitoring?
- Monitoring your Kubernetes cluster is essential for ensuring the health and performance of your applications and infrastructure. Here are some reasons why monitoring your Kubernetes cluster is important:
- Identify issues and troubleshoot: By monitoring your Kubernetes cluster, you can quickly identify issues such as application crashes, resource bottlenecks, and network problems. With real-time monitoring, you can troubleshoot issues before they escalate and impact your users.
- Optimize performance and capacity: Monitoring allows you to track the performance of your applications and infrastructure over time, and identify opportunities to optimize performance and capacity. By understanding usage patterns and resource consumption, you can make informed decisions about scaling your infrastructure and improving the efficiency of your applications.
- Ensure high availability: Kubernetes is designed to provide high availability for your applications, but this requires careful monitoring and management. By monitoring your cluster and setting up alerts, you can ensure that your applications remain available even in the event of failures or unexpected events.
- Security and compliance: Monitoring your Kubernetes cluster can help you identify potential security risks and ensure compliance with regulations and policies. By tracking access logs and other security-related metrics, you can quickly detect and respond to potential security threats.

#### Install Helm
```bash
curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
```

#### Add helm repos for Prometheus and Grafana
```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo add grafana https://grafana.github.io/helm-charts
```
#### Update helm repo
```bash
helm repo update
```
#### Helm install Prometheus and Grafana
```bash
helm install prometheus prometheus-community/prometheus
helm install grafana grafana/grafana
```
#### Expose Prometheus Service as Node Port
This is required to access from browser
```bash
kubectl expose service prometheus-server --type=NodePort --target-port=9090 --name=prometheus-server-ext
```
#### You can also expose it as with Type LoadBalancer service to access in browser with port 80
```bash
kubectl expose service prometheus-server --type=LoadBalancer --target-port=9090 --name=prometheus-server-lb-ext
```
#### Expose Grafana Service on Node Port
This is required to access in browser
```bash
kubectl expose service grafana — type=NodePort — target-port=3000 — name=grafana-ext
```
#### You can also expose it as with type LoadBalancer service to access in browser with port 80
```bash
kubectl expose service grafana — type=LoadBalancer — target-port=3000 — name=grafana-lb-ext
```
