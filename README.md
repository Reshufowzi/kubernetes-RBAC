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

