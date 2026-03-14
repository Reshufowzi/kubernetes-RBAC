
## kubectl installation in linux 

```
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"

chmod +x kubectl
sudo mv kubectl /usr/local/bin/

kubectl version
```

```
aws configure
```
```
aws eks --region us-east-1 update-kubeconfig --name mycluster
```
```
kubectl get nodes
```
```
IAM Roles for Service Accounts (IRSA) is a feature in Amazon Elastic Kubernetes Service that allows Kubernetes Pods to securely access AWS services using IAM roles.

Instead of giving AWS permissions to the entire node (EC2), IRSA allows a specific Kubernetes Service Account to assume an IAM role.

1️⃣ Why IRSA is Needed

In Kubernetes running on EKS, applications inside Pods may need to access AWS services like:

Amazon Simple Storage Service

Amazon DynamoDB

Amazon Simple Queue Service

Amazon Simple Notification Service

Earlier problem:

❌ All Pods used the same IAM role attached to the EC2 node.
❌ Every Pod had too many permissions (security risk).

IRSA solves this.

2️⃣ What IRSA Does

IRSA allows:

✔ Each Kubernetes Service Account → gets its own IAM Role
✔ Pod using that service account → gets temporary AWS credentials

So:

Pod → Service Account → IAM Role → AWS Service

Example flow:

Pod (Application)
   ↓
Kubernetes Service Account
   ↓
IAM Role (IRSA)
   ↓
Access AWS S3
3️⃣ Example Scenario

Suppose you have:

Pod running a Python app

It needs to upload files to S3

Steps:

1️⃣ Create IAM Role with S3 permissions
2️⃣ Link IAM Role to Kubernetes Service Account
3️⃣ Pod uses that Service Account
4️⃣ Pod automatically gets AWS credentials

Now only that Pod can access S3.

4️⃣ Components in IRSA
Component	Purpose
IAM Role	Gives AWS permissions
Service Account	Identity for Pod
OIDC Provider	Connects Kubernetes with AWS IAM
Pod	Uses the Service Account

OIDC is used for authentication between Kubernetes and AWS.

5️⃣ Simple Architecture
Kubernetes Pod
     │
     ▼
Service Account
     │
     ▼
IAM Role (IRSA)
     │
     ▼
AWS Service (S3 / DynamoDB / etc)
6️⃣ Advantages of IRSA

🔐 Better Security – Each Pod gets limited permissions
⚡ No AWS credentials stored in Pods
📦 Fine-grained access control
☁️ Native integration with AWS IAM

7️⃣ Real DevOps Example

In a CI/CD Kubernetes project (like your Jenkins + ArgoCD + Kubernetes pipeline):

Jenkins Pod → push image to Amazon Elastic Container Registry

Logging Pod → send logs to Amazon CloudWatch

Backup Pod → store files in Amazon Simple Storage Service

Each Pod uses different IRSA roles.

✅ One-line interview answer:

IRSA (IAM Roles for Service Accounts) is a feature in Amazon EKS that allows Kubernetes Pods to securely access AWS services by associating IAM roles with Kubernetes service accounts.
```
