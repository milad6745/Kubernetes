# lable & Selector

### Label (برچسب):

Label این برچسب‌ها به شما امکان می‌دهند تا منابع را به عنوان یک گروه مشخص کنید یا ویژگی‌های خاص را به آن‌ها اختصاص دهید. برچسب‌ها همیشه به صورت کلید-مقدار هستند و یک منبع می‌تواند چندین برچسب داشته باشد.

مثال:

```yaml
metadata:
  labels:
    app: my-app
    environment: production
```

### Selector (انتخاب‌گر):

یک عبارت است که بر اساس برچسب‌ها منابع را انتخاب می‌کند. این انتخاب‌گرها در منابع دیگر یا در عملیات‌های Kubernetes (مانند سرویس‌ها، کنترل‌کننده‌ها و دیگر منابع) استفاده می‌شوند تا منابع مورد نظر را یا یک گروه از منابع را انتخاب کنند.

مثال:

```yaml
selector:
  matchLabels:
    app: my-app
    environment: production
```

در این مثال، یک انتخاب‌گر با `matchLabels` تعریف شده است که تمام منابعی که دارای برچسب‌های `app: my-app` و `environment: production` هستند، را انتخاب می‌کند.

استفاده از برچسب و انتخاب‌گر در Kubernetes به شما این امکان را می‌دهد که برنامه را به راحتی مدیریت کنید و عملیات مختلفی مانند انتخاب، مانیتورینگ، و ایجاد ارتباط بین منابع را انجام دهید.

![image](https://github.com/milad6745/Kubernetes/assets/113288076/95a0a4b0-3b81-4b2d-866f-51f188ab2d64)
![Uploading image.png…]()


## Example
create a service & create a pod and connect pod to service with label and selector
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
  labels:
    app: my-app
    role: backend
spec:
  containers:
    - name: my-container
      image: nginx:latest
      ports:
        - containerPort: 80

# Service Manifest (service.yaml)
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  selector:
    app: my-app
    role: backend
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
```
حال میبینیم که پادمان با استفاده از label به سرویس امان متصل شده است .
```bash
#kubectl get pod mypod-label -o wide
NAME          READY   STATUS    RESTARTS   AGE   IP            NODE                          NOMINATED NODE   READINESS GATES
mypod-label   1/1     Running   0          33s   10.244.0.18   milad-cluster-control-plane   <none>           <none>

#kubectl get ep
NAME         ENDPOINTS         AGE
kubernetes   172.18.0.2:6443   6d22h
my-service   10.244.0.18:80    136m
```
## create label to running node

برای افزودن یک برچسب (label) به یک نود در Kubernetes، می‌توانید از دستور `kubectl label nodes` استفاده کنید. در زیر، یک مثال نحوه افزودن یک برچسب به یک نود نشان داده شده است:

```bash
kubectl label nodes <نام-نود> <نام-برچسب>=<مقدار-برچسب>
```

برای مثال، فرض کنید که شما می‌خواهید به نودی با نام "mynode" برچسب "environment=production" اختصاص دهید، دستور زیر را اجرا کنید:

```bash
kubectl label nodes mynode size=large
```
برای دیدن لیبل ست شده
```bash
kubectl edit node mynode
labeles:
....
size=large
```
