```bash
aws eks --region us-east-1 update-kubeconfig --name mycluster
```

### Install kubectl for Linux

```bash
curl -Lo kubectl https://dl.k8s.io/release/$(curl -s -L https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin/kubectl
```

### 🧱 eksctl Setup

```bash
# Install eksctl
ARCH=amd64
PLATFORM=$(uname -s)_$ARCH

curl -sLO "https://github.com/eksctl-io/eksctl/releases/latest/download/eksctl_$PLATFORM.tar.gz"
curl -sL "https://github.com/eksctl-io/eksctl/releases/latest/download/eksctl_checksums.txt" | grep $PLATFORM | sha256sum --check

tar -xzf eksctl_$PLATFORM.tar.gz -C /tmp && rm eksctl_$PLATFORM.tar.gz
sudo install -m 0755 /tmp/eksctl /usr/local/bin && rm /tmp/eksctl
```

### 🔐 OIDC and IAM Setup

```bash
eksctl utils associate-iam-oidc-provider \
  --region=us-east-1 \
  --cluster=mycluster \
  --approve
```

### 📥 Download the IAM policy:

[Click Me for IAM Json Policy](https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/refs/heads/main/docs/install/iam_policy.json)

Create the IAM policy in AWS as: `AWSLoadBalancerControllerIAMPolicy`

### 📥 Create service account:

```bash
eksctl create iamserviceaccount \
  --cluster mycluster \
  --region us-east-1 \
  --namespace kube-system \
  --name aws-load-balancer-controller2 \
  --attach-policy-arn arn:aws:iam::account-id:policy/AWSLoadBalancerControllerIAMPolicy \
  --approve
```
## troubleshoot Run this final verification:

```
kubectl get sa aws-load-balancer-controller2 \
-n kube-system -o yaml
```
## not came means

```
eksctl create iamserviceaccount \
  --cluster mycluster \
  --region eu-north-1 \
  --namespace kube-system \
  --name aws-load-balancer-controller2 \
  --attach-policy-arn arn:aws:iam::572885593612:policy/AWSLoadBalancerControllerIAMPolicy \
  --override-existing-serviceaccounts \
  --approve
```
```
aws cloudformation list-stacks --region eu-north-1
```
## output

## eksctl-mycluster-addon-iamserviceaccount-kube-system-aws-load-balancer-controller2

```
kubectl get sa -n kube-system
```
## output
## aws-load-balancer-controller2

```
aws cloudformation update-termination-protection \
  --no-enable-termination-protection \
  --stack-name eksctl-mycluster-addon-iamserviceaccount-kube-system-aws-load-balancer-controller2 \
  --region eu-north-1
```
```
aws cloudformation delete-stack \
  --stack-name eksctl-mycluster-addon-iamserviceaccount-kube-system-aws-load-balancer-controller2 \
  --region eu-north-1
```
```
aws cloudformation wait stack-delete-complete \
  --stack-name eksctl-mycluster-addon-iamserviceaccount-kube-system-aws-load-balancer-controller2 \
  --region eu-north-1
```
```
eksctl create iamserviceaccount \
  --cluster mycluster \
  --region eu-north-1 \
  --namespace kube-system \
  --name aws-load-balancer-controller2 \
  --attach-policy-arn arn:aws:iam::572885593612:policy/AWSLoadBalancerControllerIAMPolicy \
  --approve
```
```
kubectl get sa -n kube-system
```



### 📦 Helm Setup

```bash
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh
```
### 📡 Add and update repo:

```bash
helm repo add eks https://aws.github.io/eks-charts
helm repo update
```
```bash
aws eks describe-cluster \
  --name mycluster \
  --region us-east-1 \
  --query "cluster.resourcesVpcConfig.vpcId" \
  --output text
```

### 🚀 Install AWS ALB Ingress Controller

```bash
helm install aws-load-balancer-controller eks/aws-load-balancer-controller \
  -n kube-system \
  --set clusterName=mycluster \
  --set region=us-east-1 \
  --set vpcId=vpc-id \
  --set serviceAccount.create=false \
  --set serviceAccount.name=aws-load-balancer-controller2
```

```
apiVersion: v1
kind: Namespace
metadata:
  name: namespace1
---
apiVersion: v1
kind: Namespace
metadata:
  name: namespace2

```
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app1
  namespace: namespace1
spec:
  replicas: 2
  selector:
    matchLabels:
      app: app1
  template:
    metadata:
      labels:
        app: app1
    spec:
      containers:
      - name: app1
        image: hashicorp/http-echo
        args: ["-text=Namespace1 result"]
        ports:
        - containerPort: 5678
---
apiVersion: v1
kind: Service
metadata:
  name: namespace1-svc
  namespace: namespace1
spec:
  selector:
    app: app1
  ports:
  - port: 80
    targetPort: 5678
---
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: namespace1-ingress
  namespace: namespace1
  annotations:
    kubernetes.io/ingress.class: alb
    alb.ingress.kubernetes.io/scheme: internet-facing
    alb.ingress.kubernetes.io/target-type: ip
    alb.ingress.kubernetes.io/group.name: shared-alb
spec:
  rules:
  - http:
      paths:
      - path: /firstnamespace
        pathType: Prefix
        backend:
          service:
            name: namespace1-svc
            port:
              number: 80
```
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app2
  namespace: namespace2
spec:
  replicas: 2
  selector:
    matchLabels:
      app: app2
  template:
    metadata:
      labels:
        app: app2
    spec:
      containers:
      - name: app2
        image: hashicorp/http-echo
        args: ["-text=Namespace2 result"]
        ports:
        - containerPort: 5678
---
apiVersion: v1
kind: Service
metadata:
  name: namespace2-svc
  namespace: namespace2
spec:
  selector:
    app: app2
  ports:
  - port: 80
    targetPort: 5678
---
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: namespace2-ingress
  namespace: namespace2
  annotations:
    kubernetes.io/ingress.class: alb
    alb.ingress.kubernetes.io/scheme: internet-facing
    alb.ingress.kubernetes.io/target-type: ip
    alb.ingress.kubernetes.io/group.name: shared-alb
spec:
  rules:
  - http:
      paths:
      - path: /secondnamespace
        pathType: Prefix
        backend:
          service:
            name: namespace2-svc
            port:
              number: 80

```


