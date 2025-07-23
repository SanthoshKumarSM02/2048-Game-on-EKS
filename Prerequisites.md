# 🛠️ Prerequisites for Deploying 2048 on Kubernetes with EKS

This guide walks you through installing and configuring the required tools to deploy the 2048 game on an AWS EKS (Elastic Kubernetes Service) cluster using `kubectl`, `eksctl`, and `AWS CLI`.

---

## ✅ Tools Required

### 1. **AWS CLI**

**Why it's needed:**  

Used to interact with AWS services, including setting credentials and configuring regions.

#### 🔧 Installation:

**Go to official website download the file based on your OS**
```bash
https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html
````
#### Check the version 
```bash
aws --version
````

#### 📌 Example Output:
````bash
aws-cli/2.15.23 Python/3.11.4 Linux/5.15.0-84-generic exe/x86_64
````

#### 🔐 Configure:
````bash
aws configure
````
### Provide 
#### 1. AWS Access Key ID
#### 2. AWS Secret Access Key
#### 3. Default region (e.g., us-west-2)
#### 4. Default output format (e.g., json)


## 

### 2. **Kubectl**

**Why it's needed:**  

kubectl (pronounced "kube control") is the command-line tool for interacting with Kubernetes clusters.
It is essential when working with Kubernetes — including on Amazon EKS — because it allows you to manage and deploy applications.

#### 🔧 Installation:

**Go to official website download the file based on your OS**
```bash
https://kubernetes.io/docs/tasks/tools/
````

#### Check the version 
```bash
kubectl version --client
````

#### 📌 Example Output:
````bash
Client Version: version.Info{Major:"1", Minor:"30", GitVersion:"v1.30.1", ...}
````

##

### 3. **Eksctl**

**Why it's needed:**  
eksctl is a command-line tool built specifically to manage Amazon EKS (Elastic Kubernetes Service) clusters.

While you could create and manage EKS clusters using the AWS Console, CloudFormation, or the AWS CLI, eksctl dramatically simplifies and automates the process.



#### 🔧 Installation:

**Install Chocolatey**

**💡 Why install Chocolatey before installing eksctl (on Windows)?**

**Chocolatey is a Windows package manager — similar to apt on Ubuntu or brew on macOS. It simplifies the installation of tools like eksctl, kubectl, awscli, etc.**

```bash
Set-ExecutionPolicy Bypass -Scope Process -Force; `
[System.Net.ServicePointManager]::SecurityProtocol = `
[System.Net.ServicePointManager]::SecurityProtocol -bor 3072; `
iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))

````
#### Check the version 
```bash
choco -v
````

** 🔧 Install Eksctl**
````bash
choco install eksctl -y
````

#### Check the version 
```bash
eksctl version 
````

#### 📌 Example Output:
````bash
eksctl version: 0.180.0
GitVersion: 0.180.0
````
