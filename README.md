# 2048-Game-on-EKS
Deploying the 2048 game application on a Kubernetes cluster using kubectl, eksctl, and AWS CLI.

# Step.1 
## Create EKS Cluster

```bash
eksctl create cluster --name my-eks-cluster --region us-east-1 --fargate
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
    --cluster demo-cluster \
    --region us-east-1 \
    --name alb-sample-app \
    --namespace game-2048
```

## ✅ What this command does:
It creates a Fargate Profile for your EKS cluster named demo-cluster in the us-east-1 region.

This Fargate profile will make sure that any Pods deployed in the game-2048 namespace will run on AWS Fargate instead of EC2 instances.

---




