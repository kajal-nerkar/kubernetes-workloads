# Part 1 - Kubernetes Workloads

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
<img width="949" alt="image" src="https://github.com/user-attachments/assets/687c36be-0237-4c84-a39d-9f64b0441142">

# Part 2 - Infrastructure Plan

## Objective:
Create a plan to make workloads available publicly in a production environment, prioritizing security and developer collaboration.
```
## Technologies and Tools:
1. **Kubernetes**: Manage containerized applications.
2. **Helm**: Manage Kubernetes applications with Helm charts.
3. **Jenkins/GitLab CI/CD**: Continuous integration and delivery pipeline.
4. **GitHub/GitLab**: Source code management.
5. **Terraform**: Provision cloud infrastructure.
6. **Prometheus & Grafana**: Monitoring and visualization.
7. **ELK Stack**: Logging (Elasticsearch, Logstash, Kibana).
8. **Istio**: Service mesh security.
9. **Vault**: Manage secrets securely.
10. **Slack/Microsoft Teams**: Team communication and alerts.
```
```
## Deployment Plan:
1. **Infrastructure Setup**:
   - Use Terraform to provision cloud infrastructure.
   - Set up a Kubernetes cluster with multiple nodes.

2. **CI/CD Pipeline**:
   - Set up Jenkins or GitLab CI/CD.
   - Integrate with GitHub/GitLab for automatic builds and deployments.

3. **Application Deployment**:
   - Use Helm charts for application deployment.
   - Implement rolling updates and canary deployments.

4. **Monitoring and Logging**:
   - Deploy Prometheus and Grafana for monitoring.
   - Set up the ELK stack for centralized logging.

5. **Security Measures**:
   - Use Istio for secure microservices communication.
   - Implement Network Policies in Kubernetes.
   - Use Vault for secrets management.

6. **Developer Collaboration**:
   - Provide access to CI/CD pipeline.
   - Use Slack/Microsoft Teams for communication and alerts.
```
## Architecture Diagram:
<img width="821" alt="image" src="https://github.com/user-attachments/assets/efd03b08-0d74-486c-96f3-f262c015b72f">

## Additional Considerations:
- Implement RBAC in Kubernetes for permissions.
- Regularly update and patch components.
- Conduct periodic security audits.

By following this plan, developers can manage deployments efficiently while maintaining security and high availability.
