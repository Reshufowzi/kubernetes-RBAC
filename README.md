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

# Role Based Access Control 

### Role , RoleBinding , ClusterRole , ClusterRoleBinding , ServiceAccount 

## create the policy 
```
{
	"Version": "2012-10-17",
	"Statement": [
		{
			"Sid": "Statement1",
			"Effect": "Allow",
			"Action": ["eks:DescribeCluster"], 
			"Resource": "arn:aws:eks:us-east-1:128913199644:cluster/mycluster"
		}
	]
}
```

## create the 2 users and attach the policy 
## there will be one config file 

```
kubectl edit configmap aws-auth -n kube-system
```

## paste under the map roles

```
mapUsers: |
    - userarn: arn:aws:iam::128913199644:user/developer
      username: developer
      groups:
        - developer-grp
    - userarn: arn:aws:iam::128913199644:user/tester
      username: tester
      groups:
        - tester-grp
```

## role and rolebinnding 

## role .. create the any yaml file and apply to create 

```
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: dev-role
  namespace: developer-namespace
rules:
- apiGroups: ["", "apps", "batch"]
  resources: ["pods", "services", "deployments", "jobs"]
  verbs: ["get", "list", "create", "update", "delete", "watch"]
```

## rolebinding .. create the any yaml file and apply to create

```
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: dev-binding
  namespace: developer-namespace
subjects:
- kind: Group
  name: developer-grp
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: dev-role
  apiGroup: rbac.authorization.k8s.io
```




