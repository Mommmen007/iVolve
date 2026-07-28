# Lab 17 – Resource Requests & Limits

## 📌 Objective

In this lab, I configured CPU and Memory **Requests** and **Limits** for the Node.js application Deployment. I also learned how Kubernetes schedules Pods based on resource requests, how resource limits prevent containers from consuming excessive resources, and how QoS (Quality of Service) classes are assigned.

---

## 📂 Project Structure

```text
lab17/
├── deployment.yaml
└── README.md
```

---

## ⚙️ Steps Performed

### 1. Added Resource Requests & Limits

Updated the Node.js Deployment with the following resource configuration:

```yaml
resources:
  requests:
    cpu: "1"
    memory: "1Gi"
  limits:
    cpu: "2"
    memory: "2Gi"
```

---

### 2. Applied the Deployment

```bash
kubectl apply -f deployment.yaml -n ivolve
```

---

### 3. Verified the Deployment

Checked the Pods:

```bash
kubectl get pods -n ivolve
```

Verified the Deployment:

```bash
kubectl describe deployment nodejs-app -n ivolve
```

The deployment showed:

* CPU Request = **1**
* Memory Request = **1Gi**
* CPU Limit = **2**
* Memory Limit = **2Gi**

---

### 4. ResourceQuota Issue

The Deployment initially failed to perform a Rolling Update.

Error:

```text
deployment "nodejs-app" exceeded its progress deadline
```

The ReplicaSet showed:

```text
exceeded quota: pod-quota
```

The namespace had reached the maximum number of allowed Pods.

Current quota:

```text
Used: 5
Hard: 5
```

Since a RollingUpdate creates a new Pod before deleting an old one, Kubernetes couldn't create an additional Pod.

After increasing the Pod quota:

```text
Hard: 10
```

The rollout completed successfully.

---

### 5. Verified QoS

After applying Requests and Limits:

```bash
kubectl describe pod <pod-name> -n ivolve
```

The Pod QoS changed to:

```text
QoS Class: Burstable
```

This confirms that resource requests and limits were successfully applied.

---

### 6. Metrics Server

Running:

```bash
kubectl top pod -n ivolve
```

returned:

```text
Metrics API not available
```

This indicates that **Metrics Server** is not installed or configured in the Kubernetes cluster.

---

## 🧠 Key Concepts

### Requests

The minimum amount of CPU and Memory guaranteed for a container.

Requests are used by the Kubernetes Scheduler to determine where a Pod can run.

---

### Limits

The maximum amount of CPU and Memory a container is allowed to consume.

If the container exceeds these limits, Kubernetes enforces them.

---

### QoS Classes

Kubernetes assigns a Quality of Service class to every Pod.

| QoS Class  | Description                                     |
| ---------- | ----------------------------------------------- |
| BestEffort | No Requests or Limits defined                   |
| Burstable  | Requests and/or Limits are defined              |
| Guaranteed | Requests and Limits are equal for all resources |

---

### Rolling Update

During a Rolling Update, Kubernetes creates a new Pod before deleting an old one.

If the namespace ResourceQuota does not allow creating additional Pods, the rollout will fail until resources or quotas are adjusted.

---

## 📸 Screenshots

Add screenshots for:

* Deployment applied successfully
* `kubectl get pods`
* `kubectl describe deployment`
* `kubectl describe pod`
* `QoS Class: Burstable`
* ResourceQuota before and after modification
* Successful rollout
* Metrics API error

---

## ✅ Verification Commands

```bash
kubectl apply -f deployment.yaml -n ivolve

kubectl get pods -n ivolve

kubectl describe deployment nodejs-app -n ivolve

kubectl describe pod <pod-name> -n ivolve

kubectl rollout status deployment nodejs-app -n ivolve

kubectl describe resourcequota pod-quota -n ivolve

kubectl top pod -n ivolve
```

---

## 🎯 Interview Questions

### What is the difference between Requests and Limits?

**Requests** define the minimum CPU and Memory required by a container and are used by the scheduler.

**Limits** define the maximum CPU and Memory the container can consume during runtime.

---

### What is a QoS Class?

A QoS Class determines how Kubernetes prioritizes Pods when the node experiences resource pressure.

---

### Why did the Rolling Update fail?

Because the namespace ResourceQuota limited the maximum number of Pods. Kubernetes couldn't create a new Pod before deleting an old one.

---

### Why did `kubectl top` fail?

Because the Kubernetes cluster did not have a working Metrics Server, so the Metrics API was unavailable.

---

## 🚀 Outcome

In this lab, I learned how to:

* Configure CPU and Memory Requests.
* Configure CPU and Memory Limits.
* Understand Kubernetes QoS Classes.
* Troubleshoot failed Rolling Updates caused by ResourceQuota.
* Verify Deployment configuration using `kubectl describe`.
* Understand why Metrics Server is required for resource monitoring using `kubectl top`.
