
# GitOps Demo Project with Argo CD on WSL

## 🚀 Project Overview

This is a **beginner-friendly GitOps demo project** using:

- **GitHub**: To store Kubernetes manifests (YAML files)  
- **Argo CD**: To automatically watch GitHub repo and deploy to Kubernetes  
- **Kubernetes (Minikube)**: Local cluster running on WSL  
- **WSL (Windows Subsystem for Linux)**: Linux environment on Windows

**Goal:**  
Whenever we push a change to GitHub, Argo CD automatically detects it and updates the application running in Kubernetes.  

---

## 🛠 Tools & Installation

1. **WSL (Ubuntu recommended)**  
   Install WSL from PowerShell:

   ```bash
   wsl --install
````

2. **Git** (for version control)

   ```bash
   sudo apt update
   sudo apt install -y git curl
   ```

3. **Minikube** (local Kubernetes)

   ```bash
   curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
   sudo install minikube-linux-amd64 /usr/local/bin/minikube
   minikube start --driver=docker
   ```

4. **kubectl** (Kubernetes CLI)

   ```bash
   sudo snap install kubectl --classic
   ```

5. **Argo CD** (GitOps tool)

   ```bash
   kubectl create namespace argocd
   kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
   ```

---

## 🔑 Access Argo CD UI

1. Port-forward to access Argo CD UI:

   ```bash
   kubectl port-forward svc/argocd-server -n argocd 8080:443
   ```

2. Open browser:

   ```
   https://localhost:8080
   ```

3. Login credentials:

   * Username: `admin`
   * Password:

   ```bash
   kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
   ```

---

## 📂 Project Structure

```
argocd-demo-app/
├── deployment.yaml
├── service.yaml
└── README.md
```

### 1. `deployment.yaml`

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: my-app
        image: nginx
        ports:
        - containerPort: 80
```

### 2. `service.yaml`

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  selector:
    app: my-app
  ports:
    - port: 80
      targetPort: 80
  type: NodePort
```

---

## 📌 GitHub Setup

1. Create a GitHub repo (example: `argocd-demo-app`)
2. Push local files to GitHub:

```bash
git init
git add .
git commit -m "initial commit"
git branch -M main
git remote add origin https://github.com/YOUR-USERNAME/argocd-demo-app.git
git push -u origin main
```

---

## 🌟 Connect GitHub Repo to Argo CD

1. Open Argo CD UI → **NEW APP**
2. Fill the form:

| Field               | Value                                                  |
| ------------------- | ------------------------------------------------------ |
| App Name            | demo-app                                               |
| Project             | default                                                |
| Sync Policy         | Automatic                                              |
| Repository URL      | `https://github.com/YOUR-USERNAME/argocd-demo-app.git` |
| Revision            | main                                                   |
| Path                | ./                                                     |
| Destination Cluster | `https://kubernetes.default.svc`                       |
| Namespace           | default                                                |

3. Click **Create**

   * Argo CD will watch the repo
   * Deploy your app automatically

---

## 🔄 Test Auto-Sync / Watch Function

1. Edit `deployment.yaml`:

```yaml
replicas: 3
```

2. Commit & push:

```bash
git add .
git commit -m "update replicas to 3"
git push
```

3. Argo CD will detect and sync automatically → Pods increase to 3

Check pods:

```bash
kubectl get pods
```


