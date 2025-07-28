# 2048-Game-on-EKS
Deploying the 2048 game application on a Kubernetes cluster using kubectl, eksctl, and AWS CLI.

# Step.1 
## Create EKS Cluster

```bash
eksctl create cluster --name my-eks-cluster --region eu-north-1 --fargate
```

It will create a **fully managed EKS cluster with Fargate profiles**, meaning your pods will run on **serverless infrastructure**, not EC2 nodes.

---

### ✅ Here's a list of resources created by this command:

#### 1. **Amazon EKS Cluster**

* A Kubernetes control plane managed by AWS.
* It includes:

  * API server
  * etcd (cluster state storage)
  * Core components like scheduler and controller-manager

#### 2. **Fargate Profile**

* A Fargate profile is created and associated with your cluster.
* This allows pods matching specific namespace/selectors to be scheduled on **AWS Fargate** instead of EC2.

#### 3. **IAM Roles and Policies**

* **EKS Cluster Role**: Permissions for the control plane to manage cluster operations.
* **Fargate Pod Execution Role**: IAM role allowing Fargate to pull container images and write logs to CloudWatch.
* **Node IAM role** (if EC2 is used — in your case, only Fargate).

#### 4. **VPC and Networking Components** (if not provided)

* A new **VPC** is created by default with:

  * **3 subnets** (public or private, usually private for Fargate)
  * **Route tables**
  * **Internet Gateway (IGW)** or **NAT Gateway**, depending on the config

#### 5. **Security Groups**

* Created for communication between the control plane and Fargate pods.

#### 6. **CloudFormation Stacks**

* `eksctl` uses CloudFormation under the hood, so stacks are created to manage:

  * VPC
  * EKS control plane
  * Fargate profiles
  * IAM roles

#### 7. **ConfigMap (cluster-side)**

* `aws-auth` ConfigMap is updated to allow access via IAM users/roles.

---


# Step.2 

```bash
aws eks update-kubeconfig --name my-eks-cluster --region eu-north-1
```

---

### ✅ **What this command does:**

This command **configures your local `kubectl`** tool to connect to your **EKS (Elastic Kubernetes Service) cluster** on AWS.

---

### 🧠 **Breakdown:**

* `aws eks`: This is the AWS CLI command group for working with EKS (Elastic Kubernetes Service).
* `update-kubeconfig`: This subcommand updates your local **kubeconfig file** (usually `~/.kube/config`) so that you can interact with your EKS cluster using `kubectl`.
* `--name my-eks-cluster`: Specifies the name of the EKS cluster you want to connect to.
* `--region eu-north-1`: Tells AWS CLI the region where the cluster is deployed (e.g., Stockholm, Europe).

---

### 🔧 **What it actually does:**

* **Authenticates** with AWS.
* **Fetches the endpoint** and **authentication token** for the EKS cluster.
* **Adds or updates** a section in your `~/.kube/config` file.

---
# Step.3

# Create Fargate Profile 

```bash
eksctl create fargateprofile \
    --cluster my-eks-cluster \
    --region es-north-1 \
    --name alb-sample-app \
    --namespace game-2048
```

## ✅ What this command does:
It creates a Fargate Profile for your EKS cluster named demo-cluster in the us-east-1 region.

This Fargate profile will make sure that any Pods deployed in the game-2048 namespace will run on AWS Fargate instead of EC2 instances.

---
# Step.4
Deploy the deployment, service and Ingress
<!-- ## Deploy.yaml 

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
        kubernetes.io/os: linux

```
command to execute 
```bash
kubectl apply -f deploy.yaml
```
---
## Service.yaml
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
Command to execute
```bash
kubectl apply -f service.yaml
```
-->
```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.5.4/docs/examples/2048/2048_full.yaml
```
## ✅ What it does:
It downloads and applies a Kubernetes YAML file from the internet — specifically a sample app called "2048" — and deploys it into your EKS cluster.

## 📦 What gets created by this command:
The YAML file includes several Kubernetes objects:

1. Namespace: game-2048

2. Deployment: The 2048 app pod

3. Service: A Service that exposes the pod

4. Ingress: Defines the ALB Ingress rules, so AWS ALB (Application Load Balancer) can route external traffic to your app

---
---
## Check if your 2048 game pod is running and healthy.
```bash
kubectl get pods -n game-2048
```
---
```bash
kubectl get svc -n game-2048
```
🔍 What it does:
1. Lists all Services in the game-2048 namespace.
2. Shows how the application is exposed (internally or externally).
---
```bash
kubectl get ingress -n game-2048
```

1. This command lists all Ingress resources in the game-2048 namespace, showing how external HTTP/HTTPS traffic is routed.
2. It helps verify if your app is exposed via a domain or AWS ALB (Application Load Balancer).
---

```bash
eksctl utils associate-iam-oidc-provider --cluster my-eks-cluster --approve
```

🔐 This command enables the IAM OIDC provider for your EKS cluster.
✅ It allows Kubernetes service accounts to assume IAM roles using IRSA (IAM Roles for Service Accounts).

---
---
# Step.4
## Create IAM Policy
```bash
Invoke-WebRequest -Uri "https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.11.0/docs/install/iam_policy.json" -OutFile "iam_policy.json"
```
🔽 This downloads the IAM policy document (JSON file) used by the AWS Load Balancer Controller.

```bash
aws iam create-policy --policy-name AWSLoadBalancerControllerIAMPolicy --policy-document file://iam_policy.json
```

🔐 This step creates the policy in your AWS account so that it can later be attached to a service account used by the AWS Load Balancer Controller in your EKS cluster.
---
Purpose of these commands:
These two commands download and create an IAM policy that is required for the AWS Load Balancer Controller to function properly in an EKS (Elastic Kubernetes Service) cluster.

---

# Step.5
## Creates an IAM service account in your EKS cluster and attaches the IAM policy you created earlier (for the AWS Load Balancer Controller).

```bash
eksctl create iamserviceaccount `
  --cluster=my-eks-cluster `
  --namespace=kube-system `
  --name=aws-load-balancer-controller-1 `
  --role-name AmazonEKSLoadBalancerControllerRole `
  --attach-policy-arn=arn:aws:iam::<aws-accid>:policy/AWSLoadBalancerControllerIAMPolicy `
  --approve

```
## 🧠 What happens behind the scenes:
1. IAM Role is created (with the policy attached).
2. The role is linked to the Kubernetes service account using IAM OIDC (so pods using this service account can assume the role).
3. The service account is created inside your cluster (kube-system namespace).
4. Later, when you deploy the AWS Load Balancer Controller, you will configure it to use this service account — so it can interact with AWS services like    ELB, Target Groups, etc.

---
# Step.6

# Deploys the AWS Load Balancer Controller into your Amazon EKS cluster, using Helm, a package manager for Kubernetes.
```bash
helm repo add eks https://aws.github.io/eks-charts
```

```bash
helm repo update eks
```
```bash
helm install aws-load-balancer-controller eks/aws-load-balancer-controller -n kube-system `
  --set clusterName=my-eks-cluster `
  --set serviceAccount.create=false `
  --set serviceAccount.name=aws-load-balancer-controller `
  --set region=eu-north-1 `
  --set vpcId=<vpc-id>
```
# 🧠 What happens after this:
1. The AWS Load Balancer Controller is deployed into your EKS cluster.
2. It starts watching for Kubernetes Ingress or Service resources.
3. When it detects them, it automatically provisions:

     * Application Load Balancers (ALBs) or
     * Target Groups
     * Based on what your application needs.
       
4. It uses the IAM service account (linked to the IAM role with permissions) to call AWS APIs.

---
# Step.7
## Check the deployment and pods status
```bash
kubectl get pods -n kube-deploy
```
You run this to check which system-level services (like CoreDNS, metrics-server, AWS controllers) are deployed and whether they are running properly.

```bash
kubectl get pods -n kube-deploy
```
Useful when you want to see the actual pod-level status (Running, CrashLoopBackOff, etc.) of your workloads or controllers deployed in that custom namespace.

---
 # Final Step
 # 🔗 Accessing the 2048 Game on EKS

You can access the deployed 2048 game using either of the following methods:

### ✅ Option 1: From AWS Console

1. Go to **AWS Management Console**.
2. Navigate to **EC2 > Load Balancers**.
3. Find the **Load Balancer** created by your Ingress.
4. Copy the **DNS name**.
5. Open a browser and go to:

   ```
   http://<your-load-balancer-DNS>
   ```

---

### ✅ Option 2: From Terminal

1. Run the following command:

   ```bash
   kubectl get ingress -n game-2048
   ```
2. Copy the **ADDRESS** or **HOSTS** field from the output.
3. Paste it in your browser prefixed with `http://`:

   ```
   http://<ingress-dns-name>
   ```

---

### Example:

```
http://k8s-game2048-ingress2-3f77a602c6-1202856506.eu-north-1.elb.amazonaws.com
```

---


<img width="1847" height="1013" alt="Screenshot 2025-07-28 152741" src="https://github.com/user-attachments/assets/24d60f29-2007-48bc-9479-4a92e48c3777" />







