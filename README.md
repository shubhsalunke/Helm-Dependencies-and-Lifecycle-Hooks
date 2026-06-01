# Helm Dependencies and Lifecycle Hooks

This activity demonstrates how to use Helm Dependencies and Lifecycle Hooks in Kubernetes using MicroK8s.

---

# Project Structure

```text
helm-dependency-hooks/
│
└── myapp/
    │
    ├── Chart.yaml
    ├── Chart.lock
    ├── values.yaml
    │
    ├── charts/
    │   └── redis-20.11.4.tgz
    │
    └── templates/
        ├── deployment.yaml
        ├── service.yaml
        ├── serviceaccount.yaml
        ├── pre-install-hook.yaml
        ├── post-install-hook.yaml
        └── tests/

```

# Server Details

```bash
Server IP: SERVER-IP
```

---

# Prerequisites

- Ubuntu Server
- Internet Connection
- Sudo Privileges

---

# Step 1: Connect to Server

```bash
ssh azureuser@SERVER-IP
```

---

# Step 2: Install MicroK8s

```bash
sudo snap install microk8s --classic
```

Add current user to MicroK8s group:

```bash
sudo usermod -aG microk8s $USER
```

Create kube directory:

```bash
mkdir -p ~/.kube
sudo chown -R $USER:$USER ~/.kube
```

Logout and reconnect:

```bash
exit
```

```bash
ssh azureuser@SERVER-IP
```

---

# Step 3: Verify MicroK8s

```bash
microk8s status --wait-ready
```

Expected:

```bash
microk8s is running
```

---

# Step 4: Enable Required Addons

```bash
microk8s enable dns
```

```bash
microk8s enable hostpath-storage
```

```bash
microk8s enable helm3
```

---

# Step 5: Create Kubectl Alias

```bash
sudo snap alias microk8s.kubectl kubectl
```

Verify:

```bash
kubectl version --client
```

```bash
kubectl get nodes
```

Expected:

```bash
NAME                 STATUS   ROLES
mytfvm-fresh120335   Ready
```

---

# Step 6: Create Helm Alias

```bash
sudo snap alias microk8s.helm3 helm
```

Verify:

```bash
helm version
```

---

# Step 7: Clone Repository

```bash
git clone https://github.com/shubhsalunke/react-node-mysql-crud.git
```

```bash
cd Helm-Dependencies-and-Lifecycle-Hooks
```

Create Helm Chart:

```bash
helm create myapp
```

```bash
cd myapp
```

---

# Step 8: Add Redis Dependency

Edit Chart.yaml:

```bash
nano Chart.yaml
```

Add:

```yaml
dependencies:
  - name: redis
    version: 20.11.4
    repository: https://charts.bitnami.com/bitnami
```

---

# Step 9: Download Dependency

```bash
helm dependency update
```

Verify:

```bash
helm dependency list
```

Expected:

```bash
NAME    VERSION
redis   20.11.4
```

---


# Step 12: Install Application

```bash
helm install myapp . \
  --set redis.architecture=standalone \
  --set redis.image.registry=docker.io \
  --set redis.image.repository=bitnamilegacy/redis \
  --set redis.image.tag=7.2.5-debian-12-r4 \
  --set global.security.allowInsecureImages=true
```

---

# Step 13: Verify Deployment

Check Helm release:

```bash
helm list
```

Check dependency:

```bash
helm dependency list
```

Check Pods:

```bash
kubectl get pods
```

Expected:

```bash
myapp-xxxxx            Running
myapp-redis-master-0   Running
```

Check Jobs:

```bash
kubectl get jobs
```

Expected:

```bash
pre-install-job    Complete
post-install-job   Complete
```

---

# Step 14: Verify Hook Logs

Pre-install Hook:

```bash
kubectl logs job/pre-install-job
```

Output:

```bash
Pre install hook running
```

Post-install Hook:

```bash
kubectl logs job/post-install-job
```

Output:

```bash
Post install hook running
```

---

# Step 15: Final Verification

```bash
helm list
```

```bash
helm dependency list
```

```bash
kubectl get pods
```

```bash
kubectl get jobs
```

```bash
kubectl logs job/pre-install-job
```

```bash
kubectl logs job/post-install-job
```

---

# Step 16: Uninstall Application

```bash
helm uninstall myapp
```

Verify:

```bash
helm list
```

---

# Expected Result

- MicroK8s Installed 
- Helm Installed 
- Redis Dependency Added 
- Dependency Downloaded 
- Pre-Install Hook Executed 
- Post-Install Hook Executed 
- Helm Release Deployed 
- Redis Pod Running 
- Hook Logs Verified 
- Helm Release Removed Successfully 
