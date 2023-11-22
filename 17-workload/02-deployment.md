# Deployment
بهترین ورک لودی که میتوانیم برای اپلیکیش های Stateless اسمان استفاده کنیم Deployment است .
از  replicaset به عنوان Replication استفاده میکند و یک امتیاز خوب دارد و به ما Historyy میدهد و میتوان Rollback کرد .


![image](https://github.com/milad6745/Kubernetes/assets/113288076/daa8e837-b801-44ee-8565-d64c15918c2c)


# Example
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: hello-world
  annotations:
    kubernetes.io/change-cause: ver1
spec:
  revisionHistoryLimit: 3
  selector:
    matchLabels:
      app: hello-world-deploy
  replicas: 2
  template:
    metadata:
      labels:
        app: hello-world-deploy
        svc: example
    spec:
      containers:
        - name: hello-world
          image: ahmadrafiee/go-hello-world:1
          ports:
            - containerPort: 80
          resources:
            requests:
              cpu: 10m
              memory: 10Mi
            limits:
              memory: 20Mi
              cpu: 20m
```
```bash
kubectl get deployments.apps
NAME          READY   UP-TO-DATE   AVAILABLE   AGE
hello-world   0/2     2            0           5s
```
وقنی Deployment ایجاد میشود  در کنارش Replicaset نیز ایجاد میشود به دلیل اینکه Deployment از Replication s سرویس Replicaset استفاده میکند .
```bash
kubectl get rs
NAME                    DESIRED   CURRENT   READY   AGE
hello-world-8977c54c9   2         2         2       94s
```
```bash
kubectl get pod
NAME                          READY   STATUS    RESTARTS       AGE
hello-world-8977c54c9-2dhwb   1/1     Running   0              5m20s
hello-world-8977c54c9-bcvsp   1/1     Running   0              5m20s
```
8977c54c9 = replica id
2dhwb = Deployment ID

