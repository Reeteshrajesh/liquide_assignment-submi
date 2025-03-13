# CKYC Helm Deployment(liquide_assignment-submi)

## Overview
This project involves migrating the CKYC (Central Know Your Customer) utility service from a Docker Compose setup to a Kubernetes deployment using Helm charts. The deployment is set up on an AWS EC2 instance using a Kind Kubernetes cluster.

## Prerequisites
Ensure the following are installed on your system before proceeding:
- Docker
- Kubernetes (kubectl)
- Kind (Kubernetes in Docker)
- Helm
- Git

## Installation and Setup
### 1. Clone the Repository
```sh
git clone https://github.com/Reeteshrajesh/liquide_assignment-submi.git
cd liquide_assignment-submi
```

### 2. Install Dependencies
```sh
sudo apt update && sudo apt upgrade -y
sudo apt install -y curl wget git vim
```

### 3. Install Docker
```sh
sudo apt install -y docker.io
sudo systemctl enable --now docker
sudo usermod -aG docker $USER
newgrp docker  # Refresh group permissions
```

### 4. Install Kubernetes CLI (kubectl)
```sh
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x kubectl
sudo mv kubectl /usr/local/bin/
kubectl version --client
```

### 5. Install Kind (Kubernetes in Docker)
```sh
curl -Lo kind "https://kind.sigs.k8s.io/dl/v0.20.0/kind-linux-amd64"
chmod +x kind
sudo mv kind /usr/local/bin/
kind version
```

### 6. Install Helm
```sh
curl -fsSL https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
helm version
```

### 7. Create Kubernetes Cluster using Kind
```sh
kind create cluster --name ckyc-cluster
```

### 8. Deploy CKYC using Helm
```sh
kubectl create namespace ckyc
helm install ckyc-release ./ckyc-helm -n ckyc
```

## Troubleshooting
### Check Deployment Status
```sh
kubectl get pods -n ckyc
```

### View Logs
```sh
kubectl logs -n ckyc -l app=ckyc-release
```

### Debug Pod
```sh
POD_NAME=$(kubectl get pod -n ckyc -l app=ckyc-release -o jsonpath="{.items[0].metadata.name}")
kubectl exec -n ckyc -it $POD_NAME -- /bin/sh
```

### ConfigMap Issue: Missing Configuration File
If the application fails due to a missing `ckyc-properties.yml`, recreate the ConfigMap:
```sh
kubectl delete configmap ckyc-config -n ckyc
kubectl create configmap ckyc-config --from-file=ckyc-properties.yml -n ckyc
```

### Redeploy Application
```sh
helm uninstall ckyc-release -n ckyc
helm install ckyc-release ./ckyc-helm -n ckyc
```

## Conclusion
This guide provides steps to deploy the CKYC service on a Kubernetes cluster using Helm. Ensure all dependencies are installed correctly and troubleshoot issues using logs and ConfigMaps.

