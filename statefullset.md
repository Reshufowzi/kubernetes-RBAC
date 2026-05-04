# service_state.yaml
```
apiVersion: v1
kind: Service
metadata:
  name: mysql-headless
spec:
  clusterIP: None
  selector:
    app: mysql
  ports:
    - port: 3306
      name: mysql
```
```
kubectl apply -f mysql-headless.yaml
```
# MySQL StatefulSet YAML
```
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: mysql-stateful
spec:
  serviceName: mysql-headless
  replicas: 2
  selector:
    matchLabels:
      app: mysql

  template:
    metadata:
      labels:
        app: mysql
    spec:
      containers:
      - name: mysql
        image: mysql:8.0

        ports:
        - containerPort: 3306

        env:
        - name: MYSQL_ROOT_PASSWORD
          value: root123
        - name: MYSQL_DATABASE
          value: mydb

        volumeMounts:
        - name: mysql-storage
          mountPath: /var/lib/mysql

  volumeClaimTemplates:
  - metadata:
      name: mysql-storage
    spec:
      storageClassName: gp2
      accessModes:
        - ReadWriteOnce
      resources:
        requests:
          storage: 2Gi
```
```
kubectl apply -f mysql-stateful.yaml
```
```
kubectl get pods
```
```
kubectl exec -it mysql-stateful-0 -- mysql -uroot -p
```
```
root123
```
```
kubectl get pvc

```
```
mysql-storage-mysql-stateful-0
mysql-storage-mysql-stateful-1
mysql-storage-mysql-stateful-2
```
