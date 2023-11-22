# replicaset

در Kubernetes، ReplicaSet یک نوع منبع است که برای تضمین تعداد مشخصی از پادها (Pods) که یک کاربرد (Application) را اجرا می‌کنند، مسئول است. ReplicaSet از Resource Controller (کنترل کننده منابع) استفاده می‌کند تا تعداد پادها را بر اساس تنظیمات مشخصی ایجاد و حفظ کند.

برخلاف Deployment که یک انتزاع بالاتر از ReplicaSet است و قابلیت‌های بیشتری از جمله امکان به‌روزرسانی نسخه و Rollback را فراهم می‌کند، ReplicaSet به طور اصلی برای مدیریت تعداد اجرای یک سرویس یا کاربرد خاص می‌آید.

تعداد پادها توسط ReplicaSet با تنظیم تعداد نمونه (replicas) کنترل می‌شود. اگر تعداد واقعی پادها کمتر از تعداد مشخص شده باشد (به علت خطا یا مشکلات دیگر)، ReplicaSet تلاش می‌کند تا تعداد مورد نظر را ایجاد کند. اگر تعداد واقعی پادها بیشتر از تعداد replicas باشد، ReplicaSet سعی می‌کند تا پادهای اضافه را حذف کند.

مثال ReplicaSet:

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: example-replicaset
spec:
  replicas: 3
  selector:
    matchLabels:
      app: example-app
  template:
    metadata:
      labels:
        app: example-app
    spec:
      containers:
      - name: nginx-container
        image: nginx:latest
```

در این مثال، ReplicaSet `example-replicaset` تعداد 3 نمونه از پادها با برچسب `app=example-app` ایجاد می‌کند. Template مشخص می‌کند چگونه هر پاد باید ایجاد شود (در اینجا از تصویر `nginx:latest` استفاده شده است).

# Example
```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: hello-world-rs
spec:
  replicas: 4
  selector:
    matchLabels:
      app: hello-world-rs
  template:
    metadata:
      labels:
        app: hello-world-rs
        svc: example
    spec:
      containers:
        - name: hello-world
          image: busybox:1.28
          command: ['sh', '-c', 'echo The app is running! && sleep 3600']
          ports:
            - containerPort: 80
          resources:
            requests:
              cpu: 10m
              memory: 10Mi
```
```bash
kubectl get rs
NAME             DESIRED   CURRENT   READY   AGE
hello-world-rs   4         4         4       17h
```
```bash
kubectl get pod
NAME                   READY   STATUS    RESTARTS       AGE
hello-world-rs-49jq4   1/1     Running   17 (31m ago)   17h
hello-world-rs-f5kx5   1/1     Running   17 (31m ago)   17h
hello-world-rs-lfq69   1/1     Running   17 (31m ago)   17h
hello-world-rs-pc9gb   1/1     Running   17 (31m ago)   17h
```

