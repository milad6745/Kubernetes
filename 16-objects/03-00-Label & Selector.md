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
  name: mypodone
  labels:
    app: my-app
spec:
  containers:
    - name: my-container
      image: nginx:latest


---


apiVersion: v1
kind: Service
metadata:
  name: service1
spec:
  selector:
    app: my-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80

```
حال میبینیم که پادمان با استفاده از label به سرویس امان متصل شده است .
```bash
kubectl get ep
NAME         ENDPOINTS                       AGE
kubernetes   172.18.0.2:6443                 7d
my-service   10.244.0.18:80                  3h40m
service1     10.244.0.18:80,10.244.0.19:80   7s
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

## Example

برای ساخت دو Pod در Kubernetes که با استفاده از برچسب‌ها (Labels) و انتخاب‌گرها (Selectors) به هم متصل شوند، می‌توانیم یک سرویس (Service) ایجاد کنیم. سرویس‌ها با استفاده از برچسب‌ها، Podها را کشف کرده و آنها را به هم متصل می‌کنند. در این مثال، ما دو Pod با برچسب‌های مشابه ایجاد می‌کنیم و سپس با استفاده از یک سرویس، آنها را به هم متصل می‌کنیم.

### مراحل کار:

1. **ایجاد دو Pod با برچسب‌های مشابه**
2. **ایجاد یک سرویس (Service) برای اتصال Podها**

### 1. ایجاد دو Pod با برچسب‌های مشابه

فرض کنیم می‌خواهیم دو Pod با برچسب `app=example` ایجاد کنیم.

**Pod اول (YAML):**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-one
  labels:
    app: example
spec:
  containers:
  - name: nginx
    image: nginx
```

**Pod دوم (YAML):**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-two
  labels:
    app: example
spec:
  containers:
  - name: nginx
    image: nginx
```

برای ایجاد این Podها، فایل‌های YAML را ایجاد کرده و آنها را اجرا کنید:

```bash
kubectl apply -f pod-one.yaml
kubectl apply -f pod-two.yaml
```

### 2. ایجاد یک سرویس برای اتصال Podها

اکنون می‌توانیم یک سرویس ایجاد کنیم که از برچسب `app=example` برای اتصال به این دو Pod استفاده کند. سرویس‌ها معمولاً برای load balancing و expose کردن Podها به کار می‌روند، اما در اینجا هدف ما صرفاً اتصال این دو Pod با هم است.

**Service (YAML):**

```yaml
apiVersion: v1
kind: Service
metadata:
  name: example-service
spec:
  selector:
    app: example
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
```

این سرویس به طور خودکار تمامی Podهایی که دارای برچسب `app=example` هستند را انتخاب و آنها را به یکدیگر متصل می‌کند. در این مثال، سرویس ترافیک ورودی روی پورت 80 را به پورت 80 روی Podهای انتخاب‌شده هدایت می‌کند.

برای ایجاد این سرویس، فایل YAML را اجرا کنید:

```bash
kubectl apply -f service.yaml
```

### بررسی اتصال Podها

بعد از ایجاد سرویس، می‌توانید سرویس و Podها را بررسی کنید تا اطمینان حاصل شود که سرویس به درستی Podها را انتخاب کرده است:

```bash
kubectl get svc example-service
kubectl describe svc example-service
kubectl get pods -l app=example
```

### تست اتصال

برای آزمایش اتصال، می‌توانید از یکی از Podها به Pod دیگر با استفاده از نام سرویس `example-service` پینگ کنید یا یک درخواست HTTP ارسال کنید:

```bash
kubectl exec -it pod-one -- curl example-service
```
```bash
kubectl get ep
NAME              ENDPOINTS                       AGE
example-service   10.244.0.45:80,10.244.0.46:80   13m
```


این دستور یک درخواست HTTP به سرویس ارسال می‌کند که به طور خودکار به یکی از Podهایی که برچسب `app=example` دارند، هدایت می‌شود.

به این ترتیب، دو Pod با استفاده از برچسب‌ها و انتخاب‌گرها به هم متصل شدند و می‌توانند از طریق سرویس به یکدیگر دسترسی داشته باشند.
