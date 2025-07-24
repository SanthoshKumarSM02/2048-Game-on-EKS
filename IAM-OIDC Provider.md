# IAM-OIDC Provider
---

### ✅ What Is the **IAM OIDC Provider**?

The **IAM OIDC Provider** (OpenID Connect) is a **bridge between IAM and Kubernetes service accounts**.

It allows **Kubernetes pods** running in your EKS cluster to **assume IAM roles securely**, **without needing to hardcode AWS credentials**.

---

### 🧠 Why Do We Need It?

By default, Kubernetes doesn’t have a way to access AWS resources (like S3, DynamoDB, etc.) securely without embedding credentials.
With IAM OIDC + IAM Roles for Service Accounts (IRSA), you can solve this **securely and natively**.

---

# Commands to Configure IAM-OIDC Provider

````bash

export cluster_name=demo-cluster
````

````bash
oidc_id=$(aws eks describe-cluster --name $cluster_name --query "cluster.identity.oidc.issuer" --output text | cut -d '/' -f 5) 
````

# Check if there is an IAM OIDC provider configured already

````bash
aws iam list-open-id-connect-providers | grep $oidc_id | cut -d "/" -f4\n

````
### If not, run the below command

````bash
eksctl utils associate-iam-oidc-provider --cluster $cluster_name --approve
````

