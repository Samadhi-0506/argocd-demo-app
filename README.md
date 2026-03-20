

# 🚀 GitOps Demo Project with Argo CD

## 📄 Project Overview

This project demonstrates a **GitOps workflow** using:

* 💻 **GitHub** – stores Kubernetes manifests
* 👀 **Argo CD** – automatically watches the repository and deploys to Kubernetes
* 🐳 **Kubernetes (Minikube)** – local cluster running on WSL

**Goal:** Whenever changes are pushed to GitHub, Argo CD automatically detects and syncs the application.

---

## 📂 Project Structure

```
argocd-demo-app/
├── deployment.yaml
├── service.yaml
└── README.md
```

---

## ⚙️ Managing the Environment

### ⏸️ Shutting Down (End of Day)

1. 🛑 **Stop Minikube (Kubernetes Cluster):**

```bash
minikube stop
```

2. ❌ **Stop Argo CD port-forward (UI Access):**

If running:

```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

Press `CTRL + C` to stop.

3. 🖥 **Exit WSL:**

```bash
exit
```

---

### ▶️ Starting Again (Next Day)

1. 🐧 **Open WSL** and start Minikube:

```bash
minikube start
```

2. 🔍 **Verify cluster:**

```bash
kubectl get nodes
```

3. 🌐 **Start Argo CD UI (port-forward):**

```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

Open your browser → `https://localhost:8080`
Login credentials:

* **Username:** `admin`
* **Password:**

```bash
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```

4. ✅ **Verify Argo CD Applications:**

```bash
argocd app list
```

5. 📦 **Optional: Check running pods:**

```bash
kubectl get pods
```

---





