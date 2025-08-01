Create the deploy.yaml file for deployment

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: eks-sample-linux-deployment
  labels:
    app: eks-sample-linux-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: eks-sample-linux-app
  template:
    metadata:
      labels:
        app: eks-sample-linux-app
    spec:
      affinity:
        nodeAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
            - matchExpressions:
              - key: kubernetes.io/arch
                operator: In
                values:
                - amd64
                - arm64
      containers:
      - name: nginx
        image: public.ecr.aws/nginx/nginx:1.23
        ports:
        - name: http
          containerPort: 80
        imagePullPolicy: IfNotPresent
      nodeSelector:
        kubernetes.io/os: Linux
```
Run the command for the deployment
```yaml
kubectl apply -f deploy.yaml
```

If you apply the above `deploy.yaml` file to a Kubernetes cluster (e.g., on Amazon EKS), here’s what will happen step by step:

---

### ✅ What It Does:

1. **Creates a Deployment** named `eks-sample-linux-deployment`.
2. **Spins up 3 replicas (Pods)** running the `nginx` container (`public.ecr.aws/nginx/nginx:1.23`).
3. **Container Details:**

   * Uses the `nginx` image version `1.23`.
   * Exposes container port `80` with the name `http`.
   * `imagePullPolicy` is set to `IfNotPresent`, so it won't pull if the image already exists on the node.
4. **Scheduling Constraints:**

   * **Node Selector:** `kubernetes.io/os: Linux`

     * The Pod will only be scheduled on **Linux nodes**.
   * **Node Affinity:**

     * Pod will only be scheduled on nodes with the architecture (`kubernetes.io/arch`) of either:

       * `amd64` or
       * `arm64`

---
