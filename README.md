👉 **ArgoCD Setup and GitOps Deployment using Docker Desktop (Kubernetes)**
---

# 🚀 ArgoCD GitOps Deployment on Docker Desktop (Kubernetes)

## 📌 Project Overview

This project demonstrates how to:

* Install ArgoCD on Docker Desktop Kubernetes
* Deploy applications using GitOps principles
* Configure auto-sync and self-healing
* Understand ArgoCD UI and Application management
* Practice real-world DevOps workflow

---

# 🧠 What is GitOps?

GitOps is a modern deployment methodology where:

* Git is the single source of truth
* Infrastructure and applications are defined declaratively (YAML)
* ArgoCD continuously syncs cluster state with Git
* Changes are applied automatically

---

# 🛠 Prerequisites

* Docker Desktop (Kubernetes enabled)
* kubectl installed
* Git installed
* GitHub repository created

---

# ⚙️ Step 1: Enable Kubernetes in Docker Desktop

1. Open Docker Desktop
2. Go to **Settings → Kubernetes**
3. Enable Kubernetes
4. Apply & Restart

Verify:

```bash
kubectl get nodes
```

Expected output:

```
docker-desktop   Ready
```

---

# 📦 Step 2: Install ArgoCD

Create namespace:

```bash
kubectl create namespace argocd
```

Install ArgoCD:

```bash
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

Check pods:

```bash
kubectl get pods -n argocd
```

---

# 🌐 Step 3: Access ArgoCD UI

Port forward:

```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

Open browser:

```
https://localhost:8080
```

Get admin password:

```bash
kubectl get secret argocd-initial-admin-secret -n argocd -o jsonpath="{.data.password}" | base64 --decode
```

Login:

* Username: admin
* Password: decoded value

---

# 📁 Project Structure

```
argocd-demo/
│
├── nginx-deployment.yaml
├── service.yaml
└── application.yaml
```

---

# 📦 nginx-deployment.yaml

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-demo
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx-demo
  template:
    metadata:
      labels:
        app: nginx-demo
    spec:
      containers:
      - name: nginx
        image: nginx
        ports:
        - containerPort: 80
```

---

# 🌐 service.yaml

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-demo
spec:
  selector:
    app: nginx-demo
  ports:
    - port: 80
      targetPort: 80
  type: ClusterIP
```

---

# 🚀 application.yaml (ArgoCD Application)

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: nginx-app
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://github.com/YOUR-USERNAME/YOUR-REPO.git
    targetRevision: HEAD
    path: .
  destination:
    server: https://kubernetes.default.svc
    namespace: default
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
```

---

# 🔄 Git Push Workflow

```bash
git init
git add .
git commit -m "Initial commit"
git remote add origin https://github.com/YOUR-USERNAME/YOUR-REPO.git
git push -u origin main
```

Apply ArgoCD Application:

```bash
kubectl apply -f application.yaml
```

---

# 🎯 Expected Result

In ArgoCD UI:

* App Status → Synced
* Health Status → Healthy

---

# 🔁 Auto Sync & Self-Healing Test

1. Change replicas from 2 → 3 in Git
2. Push changes
3. ArgoCD automatically updates cluster

OR

1. Delete pod manually:

```bash
kubectl delete pod <pod-name>
```

2. ArgoCD recreates pod automatically

---

# 🔐 Security Best Practices

* Use SSO in production
* Enable TLS everywhere
* Use Sealed Secrets or Vault
* Apply RBAC with least privilege
* Enable audit logging

---

# 📈 Scaling Best Practices

* Use AppProjects for team isolation
* Use ApplicationSets for multi-environment deployment
* Use Sync Waves for ordered deployments
* Apply Resource Quotas
* Separate management and workload clusters

---

# ⚙️ Operational Best Practices

* Git as single source of truth
* PR-based approvals
* Environment promotion (dev → staging → prod)
* Monitoring with Prometheus & Grafana
* Backup and disaster recovery

---

# 🧪 Troubleshooting

Check app status:

```bash
kubectl describe application nginx-app -n argocd
```

Check logs:

```bash
kubectl logs -n argocd deploy/argocd-server
```

Refresh app from UI if OutOfSync.

---

# 🏁 Conclusion

This project demonstrates:

* End-to-end GitOps workflow
* ArgoCD installation and configuration
* Application deployment using declarative YAML
* Automated sync and self-healing
* Real-world DevOps best practices

---

# 👩‍💻 Author

Saba
DevOps & AWS Enthusiast
Learning and implementing GitOps with ArgoCD 🚀

---


Tell me which version you want next 😊
