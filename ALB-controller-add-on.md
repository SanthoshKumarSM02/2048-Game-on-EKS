# How to setup ALB Controller

````bash
curl -O https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.11.0/docs/install/iam_policy.json
````

# Create IAM Policy

````bash
aws iam create-policy \
    --policy-name AWSLoadBalancerControllerIAMPolicy \
    --policy-document file://iam_policy.json

````

# Create IAM Role

````bash

eksctl create iamserviceaccount \
  --cluster=<your-cluster-name> \
  --namespace=kube-system \
  --name=aws-load-balancer-controller \
  --role-name AmazonEKSLoadBalancerControllerRole \
  --attach-policy-arn=arn:aws:iam::<your-aws-account-id>:policy/AWSLoadBalancerControllerIAMPolicy \
  --approve

````

# Deploy ALB Controller

To deploy ALB controller You need install helm

### ✅ Why You Need to Install **Helm** Before Deploying the ALB Ingress Controller:

The **AWS ALB Ingress Controller** (now called **AWS Load Balancer Controller**) can be installed in multiple ways, but the **official and recommended** way is using **Helm**.

Here’s why:

---

### 🔧 1. **Simplifies Installation and Configuration**

* Helm is a **package manager for Kubernetes** (like apt/yum for Linux).
* It allows you to install complex applications like the AWS Load Balancer Controller with just a few lines.
* You don’t need to manually create or manage all the YAML files (Deployments, CRDs, RBAC, etc.) — Helm does that for you.

---

# Add helm repo

```` bash
helm repo add eks https://aws.github.io/eks-charts
````

# update the repo
````bash
helm repo update eks

````

# Install 

````bash
helm install aws-load-balancer-controller eks/aws-load-balancer-controller -n kube-system \
  --set clusterName=<your-cluster-name> \
  --set serviceAccount.create=false \
  --set serviceAccount.name=aws-load-balancer-controller \
  --set region=<your-region> \
  --set vpcId=<your-vpc-id>

````
# verify the deplyments

````bash
kubectl get deployment -n kube-system aws-load-balancer-controller
````
