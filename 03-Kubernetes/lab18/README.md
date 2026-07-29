# Lab 18 – Control Pod-to-Pod Traffic via NetworkPolicy

## Objective

Implement a Kubernetes NetworkPolicy to restrict access to the MySQL database, allowing only the Node.js application pods to communicate with it over the MySQL port.

---

## Technologies Used

- Kubernetes
- NetworkPolicy
- ClusterIP Headless Service
- MySQL
- Node.js

---

## Steps

### 1. Verify Pod Labels

```bash
kubectl get pods -n ivolve --show-labels
```

Verified:

- MySQL Pod → `app=mysql`
- Node.js Pods → `app=nodejs-app`

---

### 2. Create NetworkPolicy

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-app-to-mysql
  namespace: ivolve

spec:
  podSelector:
    matchLabels:
      app: mysql

  policyTypes:
  - Ingress

  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: nodejs-app
    ports:
    - protocol: TCP
      port: 3306
```

---

### 3. Apply the Policy

```bash
kubectl apply -f networkpolicy.yaml
```

---

### 4. Verify

```bash
kubectl get networkpolicy -n ivolve
```

```bash
kubectl describe networkpolicy allow-app-to-mysql -n ivolve
```

The policy targets:

- Pods labeled `app=mysql`
- Allows ingress only from `app=nodejs-app`
- Restricts access to TCP port `3306`

---

### 5. Validate Connectivity

Verified:

- MySQL Service resolves correctly through Kubernetes DNS.
- MySQL listens on port **3306**.
- Node.js application successfully connects to the database.

Application logs:

```
Attempting to connect to MySQL...
Connected to MySQL and 'ivolve' DB found.
Server started on http://0.0.0.0:3000
```

---

## Key Concepts Learned

- Kubernetes NetworkPolicy
- Pod Selector
- Ingress Rules
- Port-based Traffic Control
- Kubernetes Service Discovery
- Kubernetes DNS
- Principle of Least Privilege
- Zero Trust Networking

---

## Repository Structure

```
lab18/
├── networkpolicy.yaml
└── README.md
```
