<<<<<<< HEAD
# Kubernetes Monitoring & Failure Simulation Project

## Overview

This project demonstrates a Kubernetes monitoring setup using the **kube-prometheus-stack** Helm chart (Prometheus + Grafana + Alertmanager), with a focus on:

- Monitoring Pod CPU and memory usage
- Tracking HTTP request rates and error rates
- Monitoring Pod restarts
- Simulating partial failures and verifying recovery via ReplicaSets or HPA
- Setting up alerts for abnormal pod restarts and failed probes

---

## Table of Contents

- [Setup Instructions](#setup-instructions)
- [Architecture Diagram](#architecture-diagram)
- [Design Decisions](#design-decisions)
- [Security Incident & Resolution](#security-incident--resolution)
- [Assumptions and Known Issues](#assumptions-and-known-issues)
- [File Structure](#file-structure)

---

## Setup Instructions

### Prerequisites

- Kubernetes cluster (tested on Minikube v1.30+)
- Helm 3.x installed
- kubectl installed and configured to your cluster

### Steps

1. **Create Namespace**

   kubectl create namespace monitoring


2. **Add Helm Repositories**

   ```bash
   helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
   helm repo update
   ```

3. **Install kube-prometheus-stack**
   Disable admission webhooks to avoid TLS secret issues on Minikube:

   ```bash
   helm install monitoring prometheus-community/kube-prometheus-stack \
     --namespace monitoring \
     --set prometheusOperator.admissionWebhooks.enabled=false
   ```

4. **Verify Pods**

   ```bash
   kubectl get pods -n monitoring
   ```

5. **Access Grafana**
   Port-forward Grafana to localhost:

   ```bash
   kubectl -n monitoring port-forward svc/monitoring-grafana 3000:80
   ```

   Open your browser and go to: `http://localhost:3000`

6. **Get Grafana Admin Password**

   ```bash
   kubectl -n monitoring get secret monitoring-grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
   ```

   Default username: `admin`

7. **Deploy Your Application**
   Apply your app deployment, service, and other resources:

   ```bash
   kubectl apply -f k8s-manifests/
   ```

8. **Simulate Failure**

   ```bash
   kubectl delete pod <pod-name> -n <app-namespace>
   ```

   Observe pod restarts and metrics in Grafana.

9. **View Alerts**
   Alerts will trigger on abnormal restarts or failed probes, visible in Grafana or Alertmanager UI.

---

## Architecture Diagram

```
+-------------------+        +-----------------+        +------------------+
|  User / Clients   |  --->  |  Kubernetes     |  --->  |  Monitoring Stack |
|                   |        |  Cluster (Pods) |        |  (Prometheus &   |
|                   |        |                 |        |   Grafana)       |
+-------------------+        +-----------------+        +------------------+
        |                              |                         |
        |                              +---- Metrics ------------+
        |                              +---- Alerts -------------+
```

---

## Design Decisions

* Used **kube-prometheus-stack** Helm chart for comprehensive monitoring out of the box.
* Disabled admission webhooks for compatibility with Minikube.
* Monitored CPU/memory, HTTP request rates, pod restarts to cover critical app health metrics.
* Used ReplicaSets for automatic recovery of pods on failure.
* Set up alerts for pod restart thresholds and failed liveness/readiness probes.
* Port-forwarded Grafana locally instead of exposing it externally for security.

---

## Security Incident & Resolution

### Incident

* The `monitoring-kube-prometheus-operator` pod was stuck in `ContainerCreating` state due to missing TLS secret:

  ```
  MountVolume.SetUp failed for volume "tls-secret" : secret "monitoring-kube-prometheus-admission" not found
  ```

### Resolution

* Disabled admission webhooks in Helm values (`prometheusOperator.admissionWebhooks.enabled=false`).
* This bypassed the need for the missing secret and allowed pods to start normally.
* Alternatively, the secret could be manually created or Helm upgrade/reinstall done carefully.

---

## Assumptions and Known Issues

* Tested on Minikube local cluster; behavior might differ on managed K8s services.
* Grafana port 3000 might be in use; free the port before port-forwarding.
* Alerts thresholds are basic and may need tuning for production workloads.
* No NetworkPolicies or RBAC policies were enforced beyond defaults.
* Failure simulation via pod deletion is manual; can be automated with scripts.

---

## File Structure

```
/monitoring-project
|-- README.md
|-- helm-values/
|     |-- monitoring-values.yaml
|-- k8s-manifests/
|     |-- app-deployment.yaml
|     |-- app-service.yaml
|     |-- network-policy.yaml
|     |-- secrets.yaml
|-- policies/
|     |-- falco-policy.yaml
|     |-- kyverno-policy.yaml
|-- grafana-dashboards/
|     |-- pod-metrics.json
|     |-- http-errors.json
|-- scripts/
|     |-- failure-simulation.sh
|-- diagrams/
|     |-- architecture-ascii.txt
|     |-- architecture.png
```

---
=======
# minikube
# minikube
>>>>>>> e93d92e (jsonchanges)
