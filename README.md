# Kubernetes Workloads Challange

## Overview
This Project involves deploying a microservices architecture using Kubernetes. This architecture includes backend services, database deployments, monitoring, and network policies to ensure a secure and observable environment.

## Requirements
- Minikube
- Helm
 
## Project Structure
```

k8s-manifests/
├── backend/
│ ├── deployment.yaml     # Deployment manifest for the backend application
│ ├── service.yaml        # Service to expose the backend deployment
├── db/
│ ├── db1/
│ │ ├── deployment.yaml   # Deployment manifest for db1
│ │ ├── service.yaml      # Service to expose db1
│ ├── db2/
│ │ ├── deployment.yaml   # Deployment manifest for db2
│ │ ├── service.yaml      # Service to expose db2
│ │ ├── secret.yaml       # Secret for db2 containing username and password
├── namespace.yaml        # Namespace definition for project-plato
├── network-policy/
│ | ├── networkpolicy.yaml
├── helm/
│ | ├── postgres/         # No files needed, instructions in README.md
│ | ├── prometheus/       # No files needed, instructions in README.m
├── README.md             # Documentation for the project

```

## Prerequisites

- A running Kubernetes cluster
- `kubectl` installed and configured to interact with the cluster

## Deployment Steps
```
1. **Create the namespace:**
   kubectl apply -f namespace.yaml
2. **Deploy Backend application:**
    kubectl apply -f backend/
3. **Deploy DB1:**
    kubectl apply -f db/db1/ 
4. **Deploy DB2:**
   kubectl apply -f db/db2/
5. **Deploy Netwrokpolicy for backend:**
   kubectl apply -f network-policy/
   
## Test the status of all k8s service
  kubectl get all -n project-plato

 **Check liveness and readiness probes for backend:**
   kubectl describe pod <backend-pod-name> -n project-plato
 **Expected Output : Ensure the liveness probe (true command) and readiness probe (connectivity to db1 on port 6379) are configured and working correctly.**

## Connectivity Tests
  **Test connectivity from backend to db1 and db2:**
    kubectl exec -it <backend-pod-name> -n project-plato -- sh -c "nc -zv db1-service 6379"
    kubectl exec -it <backend-pod-name> -n project-plato -- sh -c "nc -zv db2-service 5432"
```

## Deploy Postgres and kube-prometheus-stack using Helm
```
1. **Add the Helm Repository for PostgresSQL and Prometheus**
   helm repo add bitnami https://charts.bitnami.com/bitnami
   helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
   helm repo update

2. **Deploy PostgreSQL in the project-plato namespace:**
   helm install postgres bitnami/postgresql --namespace project-plato --set primary.metrics.enabled=true --set primary.metrics.serviceMonitor.enabled=true --set 
   primary.metrics.serviceMonitor.namespace=project-plato

3. **Deploy the kube-prometheus-stack in the project-plato namespace:**
    helm install prometheus prometheus-community/kube-prometheus-stack --namespace project-plato
```
## Verify the connectivity
```
  **Check the Pods:**
    kubectl get pods -n project-plato

  **Access Prometheus UI:**
    kubectl port-forward -n project-plato svc/prometheus-kube-prometheus-prometheus 9090:9090
```




  

