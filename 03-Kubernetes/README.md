# Kubernetes Labs - Container Orchestration Fundamentals

This repository contains my Kubernetes hands-on labs completed during the Cloud DevOps Accelerator Program. These labs cover the core concepts of Kubernetes, including workload scheduling, namespaces, configuration management, persistent storage, and Stateful applications.

---

## 📂 Repository Structure

```text
.
├── lab10/
├── lab11/
├── lab12/
├── lab13/
├── lab14/
└── README.md
```

---

## 📚 Labs Included

✅ **Lab 10 – Node Isolation Using Taints**

Learned how Kubernetes controls pod scheduling using node taints and tolerations.

Configured a multi-node Kubernetes cluster.

Applied NoSchedule taints on worker nodes.

Verified scheduling behavior using kubectl.

---

✅ **Lab 11 – Namespace Management**

Created isolated namespaces.

Applied ResourceQuota.

Limited the maximum number of running Pods.

Verified quota enforcement.

---

✅ **Lab 12 – ConfigMaps & Secrets**

Created ConfigMaps for application configuration.

Created Secrets for sensitive credentials.

Injected configuration into Kubernetes workloads.

Managed application settings securely.

---

✅ **Lab 13 – Persistent Volumes & Persistent Volume Claims**

Created static PersistentVolumes.

Bound PVCs to existing PVs.

Configured HostPath storage.

Learned static provisioning concepts.

---

✅ **Lab 14 – StatefulSets & Headless Services**

Deployed MySQL using StatefulSet.

Configured persistent storage.

Created a Headless Service.

Verified stable networking and persistent data.

---

# Lab 10 - Node Isolation Using Taints

### Objective

Learn how Kubernetes controls pod scheduling by applying taints to nodes.

### Cluster

- 1 Control Plane
- 1 Worker Node

### Steps

- Create a Kubernetes cluster.
- Verify available nodes.
- Apply a `NoSchedule` taint to the worker node.
- Describe the node to verify the taint.
- Test scheduling behavior.

### Apply Taint

```bash
kubectl taint nodes desktop-worker node=worker:NoSchedule
```

### Verify

```bash
kubectl describe node desktop-worker
```

### Remove Taint

```bash
kubectl taint nodes desktop-worker node=worker:NoSchedule-
```

---

# Lab 11 - Namespace Management

### Objective

Create isolated environments and enforce resource limits using Namespaces and ResourceQuotas.

### Steps

- Create a namespace named `ivolve`.
- Apply a ResourceQuota.
- Limit the maximum number of Pods.
- Deploy test Pods.
- Verify quota enforcement.

### Create Namespace

```bash
kubectl create namespace ivolve
```

### Apply ResourceQuota

```bash
kubectl apply -f resourcequota.yaml
```

### Verify

```bash
kubectl get resourcequota -n ivolve
```

```bash
kubectl describe resourcequota -n ivolve
```

### Test

Deploy more than two Pods and verify Kubernetes rejects additional Pods.

---

# Lab 12 - ConfigMaps & Secrets

### Objective

Manage application configuration and sensitive information using ConfigMaps and Secrets.

### Components

- ConfigMap
- Secret

### ConfigMap Variables

- DB_HOST
- DB_USER

### Secret Variables

- DB_PASSWORD
- MYSQL_ROOT_PASSWORD

### Steps

- Create a ConfigMap.
- Create a Secret.
- Encode Secret values using Base64.
- Mount ConfigMap and Secret into workloads.
- Verify environment variables inside the Pod.

### Create Resources

```bash
kubectl apply -f configmap.yaml
```

```bash
kubectl apply -f secret.yaml
```

### Verify

```bash
kubectl get configmap
```

```bash
kubectl get secrets
```

### Decode Secret

```bash
echo "<base64-value>" | base64 --decode
```

### Verify Inside Pod

```bash
kubectl exec -it <pod-name> -- env
```

---

# Lab 13 - Persistent Storage with Persistent Volumes

### Objective

Configure persistent storage for Kubernetes workloads using PersistentVolumes (PV) and PersistentVolumeClaims (PVC).

### Storage Configuration

- PersistentVolume
- PersistentVolumeClaim
- HostPath Storage
- Static Provisioning

### Steps

- Create a HostPath directory on the node.
- Create a PersistentVolume.
- Configure 1Gi storage capacity.
- Set the access mode to `ReadWriteMany`.
- Set the reclaim policy to `Retain`.
- Create a PersistentVolumeClaim.
- Bind the PVC to the existing PV.
- Verify the storage binding.

### Create PersistentVolume

```bash
kubectl apply -f pv.yaml
```

### Create PersistentVolumeClaim

```bash
kubectl apply -f pvc.yaml
```

### Verify Persistent Volume

```bash
kubectl get pv
```

### Verify Persistent Volume Claim

```bash
kubectl get pvc
```

### Describe Resources

```bash
kubectl describe pv
```

```bash
kubectl describe pvc
```

### Expected Result

- PersistentVolume status becomes **Bound**.
- PersistentVolumeClaim successfully attaches to the existing PV.
- Storage remains available even if the Pod is recreated.

---

# Lab 14 - StatefulSets & Headless Services

### Objective

Deploy a MySQL database using StatefulSets with persistent storage and stable network identities.

### Components

- StatefulSet
- Headless Service
- Secret
- PersistentVolumeClaim
- Tolerations

### Steps

- Create a Secret containing MySQL credentials.
- Create a Headless Service.
- Deploy a MySQL StatefulSet.
- Mount persistent storage.
- Consume Secret values as environment variables.
- Configure tolerations for tainted worker nodes.
- Verify database connectivity.

### Create Resources

```bash
kubectl apply -f secret.yaml
```

```bash
kubectl apply -f headless-service.yaml
```

```bash
kubectl apply -f statefulset.yaml
```

### Verify Resources

```bash
kubectl get pods
```

```bash
kubectl get statefulset
```

```bash
kubectl get svc
```

```bash
kubectl get pvc
```

### Verify MySQL

```bash
kubectl exec -it mysql-0 -- mysql -uroot -p
```

### Verify DNS

```bash
nslookup mysql-0.mysql
```

### Expected Result

- MySQL Pod is running.
- Persistent storage is attached.
- Headless Service provides stable DNS.
- Database remains available after Pod recreation.

---

# Kubernetes Architecture Covered

```text
                +----------------------+
                |   Control Plane      |
                +----------+-----------+
                           |
        -------------------+-------------------
        |                                      |
+---------------+                    +---------------+
| Worker Node   |                    | Worker Node   |
|               |                    |               |
|     Pod       |                    |     Pod       |
|     PVC       |                    |               |
+-------+-------+                    +---------------+
        |
        |
+-------v-------+
| Persistent    |
| Volume (PV)   |
+---------------+
```

---

# Technologies Used

- Kubernetes
- kubectl
- YAML
- Namespaces
- ResourceQuota
- ConfigMaps
- Secrets
- PersistentVolumes
- PersistentVolumeClaims
- StatefulSets
- Headless Services
- HostPath Storage

---

# Kubernetes Concepts Covered

- Cluster Architecture
- Nodes
- Scheduling
- Taints
- Tolerations
- Namespaces
- Resource Quotas
- ConfigMaps
- Secrets
- Persistent Storage
- Persistent Volumes
- Persistent Volume Claims
- StorageClasses
- Static Provisioning
- StatefulSets
- Headless Services
- DNS Service Discovery

---

# Key Learning Outcomes

- Understanding Kubernetes node scheduling using Taints and Tolerations.
- Isolating workloads with Kubernetes Namespaces.
- Enforcing cluster resource limits using ResourceQuota.
- Managing application configuration with ConfigMaps.
- Protecting sensitive data using Kubernetes Secrets.
- Implementing persistent storage using PersistentVolumes and PersistentVolumeClaims.
- Understanding the difference between static and dynamic storage provisioning.
- Deploying stateful applications with StatefulSets.
- Configuring Headless Services for stable network identities.
- Managing persistent MySQL storage inside Kubernetes.

---

# Author

**Momen El-Gedawy**

Cloud & DevOps Engineer

Cloud DevOps Accelerator Program – iVolve Technologies
