# Prometheus and Grafana installation steps in a kubernetes cluster

![Prometheus Arcarchitecture](https://github.com/praveenece431/documents/blob/main/images/prometheus-arc.png)

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
kubectl expose service grafana --type=LoadBalancer --target-port=3000 --name=grafana-lb-ext
```

#### Expose the kube-state-metrics with type NodePort or LoadBalancer same as above
```bash
kubectl expose service prometheus-kube-state-metrics --type=LoadBalancer --target-port=8080 --name=kubestate-lb-ext
```

#### Below are the Grafana dashboard IDs that are to be used to import
#### Kubernetes Cluster Monitoring (via Prometheus):
- Dashboard ID: 1860
- Dashboard ID: 6417
- Dashboard ID: 315
- Description: 
  - Monitors your Kubernetes cluster using Prometheus. It displays overall cluster CPU, memory, and filesystem usage, as well as individual pod, container, and systemd service statistics. This dashboard relies on cAdvisor metrics only. You’ll need a running Kubernetes cluster with deployed Prometheus, which uses metrics provided by cAdvisor via the kubelet service and kube-apiserver service

#### Kubernetes Nodes Monitoring
- Dashboard ID: 15759
- Description:
  - Observes the nodes in your Kubernetes cluster, providing insights into CPU, memory, network I/O, and more.

#### Kubernetes Pods Monitoring
- Dashboard ID: 15760
- Description:
  - Offers detailed metrics for Kubernetes pods, including CPU, memory, and network I/O.

#### For all the Kube state metrics, you need to add job entry in the prometheus.yml file.
- kubectl edit cm prometheus-server
- Add the below section under scrape_configs:
  - job_name: stage_metrics
    static_configs: 
    - targets:
      - hostname:8080

### Output dashboards
![NodeExporter-Metrics](https://github.com/praveenece431/documents/blob/main/images/node-exporter.png)

![NodeMonitoring-Metrics](https://github.com/praveenece431/documents/blob/main/images/node-monitoring.png)

![PodContainer-Metrics](https://github.com/praveenece431/documents/blob/main/images/container-metrics.png)
