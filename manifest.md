# kubectl insatllation in linux 

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

# pod 
```
apiVersion: v1
kind: Pod
metadata:
  name: my-app-pod
  labels:
    app: my-app
spec:
  containers:              # ✅ must be 'containers'
    - name: my-container
      image: httpd:latest
      ports:
        - containerPort: 80
```
# service 
```
apiVersion: v1
kind: Service
metadata:
  name: my-app-service
spec:
  selector:
    app: my-app
  type: NodePort
  ports:
    - protocol: TCP
      port: 80        # Service port
      targetPort: 80  # Pod container port
      nodePort: 30007 # NodePort (must be between 30000-32767)
```
# Deployment 
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app-deployment
spec:
  replicas: 5
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
        - name: my-container
          image: nginx:latest
          ports:
            - containerPort: 80
```
# Daemontset
```
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: my-daemonset
  labels:
    app: my-daemon-app
spec:
  selector:
    matchLabels:
      app: my-daemon-app
  template:
    metadata:
      labels:
        app: my-daemon-app
    spec:
      containers:
      - name: nginx-container
        image: nginx:latest
        ports:
        - containerPort: 80
```
# To see most of the running resources in Kubernetes:
```
kubectl get all
```
```
This shows:

Pods
Services
Deployments
ReplicaSets

```
```
kubectl get all -n <namespace>

```
```
kubectl get all --all-namespaces
```
# To list ALL resource types first

```
kubectl api-resources
```

```
kubectl get configmaps
kubectl get secrets
kubectl get ingress
kubectl get pvc
kubectl get nodes

```
```
kubectl get all,configmap,secret,pvc,ingress --all-namespaces
```
```
kubectl get nodes
kubectl get namespaces
kubectl get storageclass

```
```
kubectl get all → quick check
--all-namespaces → debugging production issues
kubectl describe <resource> → deep inspection
kubectl logs <pod> → application logs

```

