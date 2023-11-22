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

## Roll back from Deployment
مثلا میخواهیم ورژن Nginx امان را ارتقا دهیم . برای این منظور فایل YAML را باز میکنیم و سپس ورژن image را در فایل عوض میکنیم و سپس :
```
kubectl apply -f <yaml file>
```
سپس مشاهده میکنیم که یه سری از pod هایمان دلیت شده و یه سری جدید ایجاد میشوند .`
```
#kubectl get pod
NAME                           READY   STATUS              RESTARTS       AGE
hello-world-794f564865-blfl8   0/1     ContainerCreating   0              88s
hello-world-8977c54c9-2dhwb    1/1     Running             0              95m
```
حالا اگر ما kubectl get rs بزنیم 2 تا Replicaset میبینیم که یکی آپدیت قدیمی و یکی آپدیت جدیدمان است .
```
kubectl get rs
NAME                     DESIRED   CURRENT   READY   AGE
hello-world-794f564865   2         2         2       7m10s
hello-world-8977c54c9    0         0         0       101m
```

با این کامند به ورژن Deployment قبلی برمی گردد .
```
kubectl rollout undo deployment hello-world
```

## rollout history
این کامند ورژن کارهایی که انجام شده است را نشان میدهد و میتوان به آن بازگشت .


```
kubectl rollout history  deployment  hello-world
deployment.apps/hello-world
REVISION  CHANGE-CAUSE
2         ver1
5         ver1
6         ver1
```
و با این کامند به ورژن دو برمیگردیم
```
kubectl rollout undo deployment hello-world --to-revision=2
```

## scale deployment
 تغییر در تعداد replica و scale کردن Deployment
 ```
 kubectl scale deployment hello-world --replicas 2
```

# تغییر در تعداد history در Deployment

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: نام-Deployment
spec:
  replicas: تعداد-Replica
  selector:
    matchLabels:
      app: نام-اپلیکیشن
  template:
    metadata:
      labels:
        app: نام-اپلیکیشن
    spec:
      containers:
      - name: نام-کانتینر
        image: تصویر-کانتینر:تگ
        ports:
        - containerPort: پورت
  revisionHistoryLimit: <تعداد>
```
