# kubernetes-RBAC

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

### Created the 5 instances and the 2 is for the EKS and the other 3 is the accessing the developer and tester and the root 

## developer user full permission in one namespace 
## tester only view permission in all namespace or certain namespace 

### Role Based Access Control 

# Role , RoleBinding , ClusterRole , ClusterRoleBinding , ServiceAccount 



