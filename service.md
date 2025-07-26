## copy the file as service.yaml

```yaml
apiVersion: v1
kind: Service
metadata:
  name: eks-sample-linux-service
  labels:
    app: eks-sample-linux-app
spec:
  selector:
    app: eks-sample-linux-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
```

---

## 📦 What This YAML Does

This creates a **Kubernetes Service** of the default type `ClusterIP` (because you didn’t specify `type:`), which:

* **Exposes port 80 inside the cluster**
* **Forwards traffic** to **Pods with label `app: eks-sample-linux-app`**
* **Maps Service port `80` → Pod port `80` (nginx)**

---
 # command to deploy the service

```yaml
kubectl apply -f service.yaml
```
