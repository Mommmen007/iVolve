# Lab 19 — Node-Wide Pod Management with DaemonSet

## Objective

Deploy Prometheus Node Exporter as a Kubernetes DaemonSet so that a Node Exporter Pod runs on every cluster node.

The DaemonSet is configured to tolerate existing node taints, and the Node Exporter metrics endpoint is verified on port `9100`.

---

## 1. Create Monitoring Namespace

```bash
kubectl create namespace monitoring
```

Verify:

```bash
kubectl get namespace monitoring
```

---

## 2. Create Node Exporter DaemonSet

Create `node-exporter.yaml`:

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: node-exporter
  namespace: monitoring
spec:
  selector:
    matchLabels:
      app: node-exporter

  template:
    metadata:
      labels:
        app: node-exporter

    spec:
      hostNetwork: true
      hostPID: true

      tolerations:
        - operator: Exists

      containers:
        - name: node-exporter
          image: prom/node-exporter:v1.9.1
          args:
            - --path.rootfs=/host

          ports:
            - name: metrics
              containerPort: 9100
              hostPort: 9100

          volumeMounts:
            - name: rootfs
              mountPath: /host
              readOnly: true

      volumes:
        - name: rootfs
          hostPath:
            path: /
```

Apply:

```bash
kubectl apply -f node-exporter.yaml
```

---

## 3. Verify DaemonSet

```bash
kubectl get daemonset -n monitoring
```

Expected result:

```text
NAME            DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE
node-exporter   2         2         2       2            2
```

The desired number of Pods equals the number of cluster nodes because a DaemonSet schedules one Pod per node.

---

## 4. Verify Pods on All Nodes

```bash
kubectl get pods -n monitoring -o wide
```

Example:

```text
NAME                  READY   STATUS    NODE
node-exporter-rd7ts   1/1     Running   desktop-control-plane
node-exporter-wbt9q   1/1     Running   desktop-worker
```

This confirms that Node Exporter is running on every node.

---

## 5. Verify Node Exporter Metrics

Forward port `9100`:

```bash
kubectl port-forward -n monitoring pod/node-exporter-rd7ts 9100:9100
```
<img width="1167" height="190" alt="image" src="https://github.com/user-attachments/assets/2740854c-4dc1-40ab-ae61-afc3e1584b34" />

Then test the metrics endpoint:

```bash
curl -s http://localhost:9100/metrics
```

To display only Node Exporter metrics:

```bash
curl -s http://localhost:9100/metrics | grep '^node_' | head -20
```

Example output:

```text
node_arp_entries{device="eth0"} 4
node_boot_time_seconds ...
node_context_switches_total ...
node_cooling_device_cur_state ...
```

The `node_*` metrics confirm that Node Exporter is successfully exposing node metrics.

---

## Key Concepts

### DaemonSet

A DaemonSet ensures that a Pod runs on every eligible node in the cluster.

```text
Node 1 → Node Exporter
Node 2 → Node Exporter
Node 3 → Node Exporter
```

### Toleration

The DaemonSet uses:

```yaml
tolerations:
  - operator: Exists
```

This allows the Node Exporter Pod to tolerate the existing taints, including the `node=worker:NoSchedule` taint used in the previous lab.

### Node Exporter

Node Exporter exposes system and node-level metrics in Prometheus format through port `9100`.

---

## Verification Summary

* [x] `monitoring` namespace created
* [x] Node Exporter DaemonSet deployed
* [x] DaemonSet scheduled a Pod on each node
* [x] Existing node taints tolerated
* [x] Node Exporter Pods running successfully
* [x] Port `9100` verified
* [x] `/metrics` endpoint verified
* [x] `node_*` Prometheus metrics successfully retrieved

## Result

Lab 19 completed successfully.

The Kubernetes cluster now has a Node Exporter instance running on each node, ready to expose node-level metrics for monitoring.

