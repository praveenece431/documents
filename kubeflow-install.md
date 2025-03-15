# Kubeflow Installation Guide

## Prerequisites
Before installing Kubeflow, ensure the following prerequisites are met:

- **Kubernetes Cluster**: A running Kubernetes cluster (v1.24 or later is recommended).
- **kubectl**: Installed and configured to interact with your Kubernetes cluster.
- **kustomize**: Installed on your system. Follow the steps below to install kustomize if not already installed:
  ```bash
  curl -LO "https://github.com/kubernetes-sigs/kustomize/releases/download/kustomize/v5.1.1/kustomize_v5.1.1_linux_amd64.tar.gz"
  tar -xvf kustomize_v5.1.1_linux_amd64.tar.gz
  sudo mv kustomize /usr/local/bin/
  kustomize version

  Storage Class: Ensure your cluster has a default storage class configured.
Ingress Controller: Install an ingress controller (e.g., NGINX Ingress) if you want to expose Kubeflow externally.
Steps to Install Kubeflow
1. Clone the Kubeflow Manifests Repository
Clone the official Kubeflow manifests repository:

bash
Copy
Edit
git clone https://github.com/kubeflow/manifests.git
cd manifests
2. Deploy the Full Kubeflow Stack
Use kustomize to build and apply the manifests for the full Kubeflow installation:

bash
Copy
Edit
while ! kustomize build example | kubectl apply -f -; do echo "Retrying to apply resources"; sleep 10; done
This will deploy all the components of Kubeflow, including:

Central Dashboard
Kubeflow Pipelines
Jupyter Notebooks
Katib (Hyperparameter Tuning)
KFServing (Model Serving)
TensorBoard
3. Verify the Installation
Check the status of the pods in the kubeflow namespace:

bash
Copy
Edit
kubectl get pods -n kubeflow
Wait until all the pods are in the Running or Completed state.

4. Access the Kubeflow Dashboard
With Ingress Controller: Access the dashboard via the ingress URL.
Without Ingress Controller: Use port-forwarding to access the dashboard:
bash
Copy
Edit
kubectl port-forward svc/istio-ingressgateway -n istio-system 8080:80
Open http://localhost:8080 in your browser.
Optional: Deploy Specific Components
If you want to deploy only specific components, use kustomize to build and apply the corresponding manifests. For example:

Deploy Kubeflow Pipelines:
bash
Copy
Edit
kustomize build pipelines | kubectl apply -f -
Deploy Jupyter Notebooks:
bash
Copy
Edit
kustomize build notebooks | kubectl apply -f -
Post-Installation Configuration
Authentication: Configure authentication (e.g., Dex or OIDC) for secure access.
Storage: Set up persistent storage for notebooks and pipelines.
Monitoring: Install Prometheus and Grafana for monitoring.
Scaling: Configure horizontal pod autoscaling (HPA) for resource optimization.
