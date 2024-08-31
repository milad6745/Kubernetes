## rollout
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80
```

```bash
kubectl rollout status deployment nginx-deployment
deployment "nginx-deployment" *successfully* rolled out
```
بررسی وضعیت راه‌اندازی: این دستور به شما می‌گوید که آیا فرآیند راه‌اندازی (rollout) Deployment با موفقیت انجام شده است یا خیر.

گزارش مشکلات احتمالی: اگر مشکلی در فرآیند راه‌اندازی وجود داشته باشد (مثلاً پادها نتوانند به وضعیت سالم برسند)، این دستور می‌تواند اطلاعاتی در مورد آن به شما ارائه دهد.

صبر کردن برای تکمیل: دستور تا زمانی که فرآیند راه‌اندازی کامل نشده باشد، به شما اطلاع نمی‌دهد. به عبارت دیگر، تا زمانی که Deployment به حالت مطلوب نرسد (تعداد پادهای مورد انتظار به درستی اجرا نشود)، دستور اجرا می‌ماند.


## rollouttest
عوض کردن ورژن:

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80
```
مشاهده میشود که پاد جدید در حال ایجاد است و در صورتی که درست ایجاد شود پاد های قبلی پاک میشود .
```
kubectl get all
NAME                                    READY   STATUS              RESTARTS   AGE
pod/nginx-deployment-576477d75f-7jwlt   0/1     ContainerCreating   0          94s
pod/nginx-deployment-86dcfdf4c6-67lfj   1/1     Running             0          20m
pod/nginx-deployment-86dcfdf4c6-gczvg   1/1     Running             0          20m
pod/nginx-deployment-86dcfdf4c6-w7tjd   1/1     Running             0          20m

NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   20m

NAME                               READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/nginx-deployment   3/3     1            3           20m

NAME                                          DESIRED   CURRENT   READY   AGE
replicaset.apps/nginx-deployment-576477d75f   1         1         0       94s
replicaset.apps/nginx-deployment-86dcfdf4c6   3         3         3       20m
replicaset.apps/nginx-deployment-94bd5979     0         0         0       8m53s
replicaset.apps/nginx-deployment-c5b86c7bf    0         0         0       9m25s
root@myubuntu:~/kuber# kubectl get all
```
تاریخچه میگیریم از جابجایی هایمان
```
kubectl rollout history deployment nginx-deployment
deployment.apps/nginx-deployment
REVISION  CHANGE-CAUSE
2         <none>
4         <none>
6         <none>
7         <none>
```
ورژن زیادتر رو داخلش هستیم یکی قبلترش میشه 6 ، برگشت به ورژن قبل

```
 kubectl rollout undo deployment nginx-deployment --to-revision=6
```
