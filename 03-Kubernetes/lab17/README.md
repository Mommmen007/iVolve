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
<img width="1052" height="135" alt="image" src="https://github.com/user-attachments/assets/f0705279-272a-4723-8ed0-a55ca174f472" />

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


<img width="1078" height="466" alt="image" src="https://github.com/user-attachments/assets/d65213fc-77a7-4206-8b88-9949f2a014c0" />

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
<img width="1435" height="152" alt="image" src="https://github.com/user-attachments/assets/d4cc4dcf-ba1a-48fd-aa5b-61ee35c2f0f9" />


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

<img width="1469" height="210" alt="image" src="https://github.com/user-attachments/assets/ee655636-96a7-4b9c-8e23-6434f8c1b854" />

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

<img width="605" height="241" alt="image" src="https://github.com/user-attachments/assets/92e254a8-6f14-41d3-a0db-f829b4ef8c85" />

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
<img width="1073" height="87" alt="image" src="https://github.com/user-attachments/assets/563a899c-42cf-4b3b-b3b8-6baa445fa970" />


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



## 🚀 Outcome

In this lab, I learned how to:

* Configure CPU and Memory Requests.
* Configure CPU and Memory Limits.
* Understand Kubernetes QoS Classes.
* Troubleshoot failed Rolling Updates caused by ResourceQuota.
* Verify Deployment configuration using `kubectl describe`.
* Understand why Metrics Server is required for resource monitoring using `kubectl top`.
