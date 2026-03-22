# claude_ML_project2: Jupyter Notebook on Kubernetes

> Foundational ML environment — Jupyter scipy-notebook deployed on Kubernetes (Docker Desktop)  
> with NodePort access and 5GiB persistent storage.

---

## Overview

This project deploys a **JupyterLab** environment on Kubernetes, pre-loaded with the full `scipy-notebook` stack (NumPy, Pandas, Scikit-learn, Matplotlib, SciPy and more). It is based on the foundational ML work in [Machine-Learning-Algorithms-for-Beginner-s](https://github.com/kiranch97/Machine-Learning-Algorithms-for-Beginner-s).

All notebooks and data saved inside `/home/jovyan/work` are persisted to a **5GiB PersistentVolumeClaim** using the default Docker Desktop `hostpath` StorageClass — so your work survives pod restarts.

---

## Architecture

```
+--------------------------------------------------------------+
|              Docker Desktop Kubernetes Cluster               |
|                                                              |
|   Namespace: jupyter-ml                                      |
|                                                              |
|   +--------------------------------------------------+       |
|   |  Deployment: jupyter                             |       |
|   |  Image: jupyter/scipy-notebook:latest           |       |
|   |  Replicas: 1                                    |       |
|   |                                                  |       |
|   |  Container: jupyter                              |       |
|   |    Port: 8888                                   |       |
|   |    Token: claude-ml-2026                        |       |
|   |    Mount: /home/jovyan/work  <-- PVC            |       |
|   +--------------------------------------------------+       |
|            |                        |                        |
|   +------------------+   +---------------------+            |
|   | Service:         |   | PVC: jupyter-pvc    |            |
|   | jupyter-nodeport |   | Storage: 5Gi        |            |
|   | Type: NodePort   |   | Class: hostpath     |            |
|   | Port: 30088      |   | Mode: ReadWriteOnce |            |
|   +------------------+   | Status: Bound       |            |
|            |              +---------------------+            |
+------------|----------------------------------------------+  |
             |
    localhost:30088
             |
        Your Browser
        (JupyterLab UI)
```

---

## Directory Structure

```
claude_ML_project2/
├── README.md
└── k8s/
    ├── namespace.yaml         # Namespace: jupyter-ml
    ├── pvc.yaml               # PersistentVolumeClaim: 5Gi
    ├── deployment.yaml        # Jupyter scipy-notebook Deployment
    └── service-nodeport.yaml  # NodePort Service on port 30088
```

---

## Pre-installed Python Packages

The `jupyter/scipy-notebook` image includes:

| Package | Use Case |
|---------|----------|
| NumPy | Numerical computing, arrays |
| Pandas | Data manipulation, DataFrames |
| Scikit-learn | ML algorithms (classification, regression, clustering) |
| Matplotlib | Data visualization |
| Seaborn | Statistical visualization |
| SciPy | Scientific computing |
| Statsmodels | Statistical models |
| JupyterLab | Web-based notebook IDE |

Perfect for running foundational ML notebooks like:
- Linear Regression
- Decision Trees
- K-Means Clustering
- Classification algorithms

---

## Quick Start

### Prerequisites
- Docker Desktop with Kubernetes enabled
- `kubectl` configured to `docker-desktop` context

```bash
kubectl config use-context docker-desktop
```

### Deploy

```bash
# Clone the repo
git clone https://github.com/kiranch97/claude_ML_project2.git
cd claude_ML_project2

# Apply all manifests
kubectl apply -f k8s/

# Watch the pod come up
kubectl get pods -n jupyter-ml -w
```

---

## Access Details

| Item | Value |
|------|-------|
| **URL** | http://localhost:30088 |
| **Token** | `claude-ml-2026` |
| **Interface** | JupyterLab (full IDE) |
| **NodePort** | 30088 |

Open your browser and navigate to:

```
http://localhost:30088
```

When prompted for a token, enter: **`claude-ml-2026`**

Or use the direct URL with token:

```
http://localhost:30088/?token=claude-ml-2026
```

---

## Persistent Storage

Your notebooks and data are saved at `/home/jovyan/work` inside the pod, which is backed by a **5GiB PVC** using Docker Desktop's `hostpath` StorageClass.

| Property | Value |
|----------|-------|
| PVC Name | `jupyter-pvc` |
| Capacity | 5Gi |
| StorageClass | `hostpath` (Docker Desktop default) |
| Access Mode | ReadWriteOnce |
| Mount Path | `/home/jovyan/work` |
| Status | Bound ✅ |

> All notebooks saved in the `work/` folder will survive pod restarts and redeployments.

---

## Verification Commands

```bash
# Check all resources
kubectl get all -n jupyter-ml

# Check PVC is Bound
kubectl get pvc -n jupyter-ml

# Check pod logs
kubectl logs -n jupyter-ml deployment/jupyter

# Get the Jupyter token from logs (if needed)
kubectl logs -n jupyter-ml deployment/jupyter | grep token
```

---

## Resource Limits

| Resource | Request | Limit |
|----------|---------|-------|
| CPU | 250m | 1000m (1 core) |
| Memory | 512Mi | 2Gi |

---

## Cleanup

```bash
kubectl delete -f k8s/
```

> Note: Deleting the PVC will erase all saved notebooks. Back up your work first!

---

## Next Steps

- Upload foundational notebooks from [Machine-Learning-Algorithms-for-Beginner-s](https://github.com/kiranch97/Machine-Learning-Algorithms-for-Beginner-s) into the `work/` folder
- Add Ingress routing (integrate with `claude-project1` nginx-ingress on port 30080)
- Scale up resources for heavier ML workloads
- Add GPU support for deep learning

---

## Author

**kiranch97** — Deployed collaboratively with Claude AI  
Date: March 2026
