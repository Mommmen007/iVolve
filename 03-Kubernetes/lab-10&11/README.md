# Lab 10 & 11 - Kubernetes Taints, Namespaces & Resource Quotas

## 📌 Overview

This lab introduces two fundamental Kubernetes concepts:

- **Lab 10:** Isolating nodes using **Taints** to control Pod scheduling.
- **Lab 11:** Organizing workloads with **Namespaces** and limiting resources using **Resource Quotas**.

---

# 🧰 Technologies Used

- Kubernetes v1.34
- Docker Desktop Kubernetes (Kind Provisioner)
- kubectl

---

# 📂 Project Structure

```text
lab10/
├── README.md
├── taint-node.sh
├── screenshots/
│   ├── cluster-nodes.png
│   ├── taint-worker.png
│   ├── verify-taint.png
│   ├── namespace-created.png
│   ├── resourcequota-created.png
│   ├── quota-verification.png
│   └── quota-limit.png
```

---

# 🚀 Lab 10 - Node Isolation Using Taints

## Objective

Prevent Pods from being scheduled on a specific worker node using Kubernetes **Taints**.

---

## Cluster Topology

```text
+-------------------------+
| Control Plane           |
| (Scheduling Allowed)    |
+-------------------------+

+-------------------------+
| Worker Node             |
| node=worker:NoSchedule  |
+-------------------------+
```

---

## Verify Cluster Nodes

```bash
kubectl get nodes
```

---

## Apply Taint

```bash
kubectl taint nodes desktop-worker node=worker:NoSchedule
```

---

## Verify Taint

```bash
kubectl get nodes -o custom-columns=NAME:.metadata.name,TAINTS:.spec.taints
```

Example Output

```text
NAME                    TAINTS
desktop-control-plane   <none>
desktop-worker          node=worker:NoSchedule
```

---

## Remove Taint

```bash
kubectl taint nodes desktop-worker node=worker:NoSchedule-
```

---

## Key Concept

A **Taint** tells Kubernetes Scheduler:

> "Do not schedule Pods on this node unless they have a matching Toleration."

---

# 🚀 Lab 11 - Namespace & Resource Quota

## Objective

- Create an isolated namespace.
- Limit the namespace to a maximum of **2 Pods**.

---

## Create Namespace

```bash
kubectl create namespace ivolve
```

Verify

```bash
kubectl get namespaces
```

---

## ResourceQuota Manifest

```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: pod-quota
  namespace: ivolve

spec:
  hard:
    pods: "2"
```

Apply

```bash
kubectl apply -f resourcequota.yaml
```

---

## Verify Resource Quota

```bash
kubectl describe resourcequota pod-quota -n ivolve
```

Example

```text
Resource   Used   Hard

pods       2      2
```

---

## Test the Quota

Create first Pod

```bash
kubectl run nginx1 --image=nginx -n ivolve
```

Create second Pod

```bash
kubectl run nginx2 --image=nginx -n ivolve
```

Attempt to create a third Pod

```bash
kubectl run nginx3 --image=nginx -n ivolve
```

Expected Result

```text
Error from server (Forbidden)

exceeded quota: pod-quota
```

---

# 📸 Screenshots

- Kubernetes Cluster (2 Nodes)
- Worker Taint Applied
- Taint Verification
- Namespace Creation
- ResourceQuota Applied
- ResourceQuota Verification
- Quota Limit Error

---

# 🎯 Learning Outcomes

After completing these labs, I learned how to:

- Configure a multi-node Kubernetes cluster.
- Apply and remove node taints.
- Understand how Kubernetes Scheduler reacts to taints.
- Create isolated namespaces.
- Apply ResourceQuota policies.
- Restrict workloads inside a namespace.
- Verify Kubernetes resources using kubectl.

---

# 🧹 Cleanup

Delete test Pods

```bash
kubectl delete pods --all -n ivolve
```

Delete ResourceQuota

```bash
kubectl delete resourcequota pod-quota -n ivolve
```

Delete Namespace

```bash
kubectl delete namespace ivolve
```

Remove Worker Taint

```bash
kubectl taint nodes desktop-worker node=worker:NoSchedule-
```

---

## 👨‍💻 Author

**Momen Elgedawy**

Cloud & DevOps Engineer
