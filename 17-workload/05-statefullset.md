# statefullset

`StatefulSet` 

یک منبع کنترل کوبرنتیز (Kubernetes) است که برای مدیریت و ایجاد اپلیکیشن‌های Stateful استفاده می‌شود. در مقابل Deployment که برای ایجاد و مدیریت اپلیکیشن‌های Stateless استفاده می‌شود، `StatefulSet` برای اپلیکیشن‌هایی که دارای وضعیت (stateful) هستند و برای ذخیره داده‌های پایدار نیاز دارند، طراحی شده است.

تفاوت اصلی `StatefulSet` با `Deployment` در این است که `StatefulSet` تلاش می‌کند تا هر Pod را با یک شناسه یکتا متناظر کند و این شناسه‌ها حتی پس از تغییرات در مقیاس، به ترتیب افزایش یا کاهش، حفظ می‌شوند.

ویژگی‌های `StatefulSet` عبارتند از:

**شناسه یکتا (Unique Identity):**
   هر Pod در یک `StatefulSet` دارای یک شناسه یکتا (unique identity) است که از یک پیشوند ثابت و یک عدد شماره‌گذاری شده تشکیل شده است. این شناسه یکتا باعث می‌شود که حتی پس از تغییرات در مقیاس `StatefulSet`، نام Pod ها حفظ شود.

**دسترسی پایدار به ذخیره داده:**
   `StatefulSet` از Volumes استفاده می‌کند تا به Pod ها دسترسی پایدار به ذخیره داده‌ها را فراهم کند. این امکان به اپلیکیشن‌های دارای وضعیت (stateful) که نیاز به ذخیره داده‌های پایدار دارند، اجازه می‌دهد تا در صورت تغییر Pod یا اعمال تغییرات در مقیاس، داده‌ها حفظ شوند.

**اجرای ترتیبی (Ordered Execution):**
   Pod ها در یک `StatefulSet` به ترتیب شماره‌گذاری می‌شوند و اجرای آن‌ها توسط `StatefulSet` ترتیب دارد. این به اپلیکیشن اجازه می‌دهد تا به صورت ایمن تر و با توجه به وضعیت خود اجرا شود، به خصوص در مواقعی که وابستگی به وضعیت یک Pod به وضعیت دیگری دارد.

**عدم نیاز به استفاده از LoadBalancer:**
   در بسیاری از موارد، اپلیکیشن‌های StatefulSet نیازی به استفاده از LoadBalancer ندارند، زیرا هر Pod می‌تواند یک IP یا نام DNS مستقل داشته باشد.

مثال یک `StatefulSet` ساده:

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: web
spec:
  serviceName: "nginx"
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
  volumeClaimTemplates:
  - metadata:
      name: www
    spec:
      accessModes: [ "ReadWriteOnce" ]
      resources:
        requests:
          storage: 1Gi
```

در این مثال، `StatefulSet` با نام "web" ایجاد شده است که شامل 3 Replica از اپلیکیشن Nginx می‌باشد. هر Replica دارای یک Volume Claim به نام "www" است که به Pod‌ها اجازه می‌دهد داده‌های پایدار را ذخیره کنند.


## Example
مشاهده میشود که در اینجا ordering برای اسم دارد و به ترتیب است و در صورتی که مثلا دلیت شود یا Replica بخواهد کم شود از 4 شروع به کم شدن میکند .
همچنین در صورتیکه manifest بالا را اجرا گنیم به ترتیم web0 را اجرا میکند و پس از رانینگ شدن بعد web 1 , ...

```
kubectl get pod
web-0         1/1     Running   0          21s
web-1         1/1     Running   0          14s
web-2         1/1     Running   0          9s
```
## موارد قابل استفاده Statefullset

از StatefulSet در Kubernetes برای مواردی استفاده می‌شود که نیاز به مدیریت وضعیت (state) دارند و از دیدگاه داده‌ها، اهمیت به ترتیب و شناسه یکتا دارند. موارد زیر از جمله استفاده‌های معمول StatefulSet در Kubernetes هستند. اگر اپلیکیشن شما نیاز به اجرای مرتب (ordered execution) با توجه به شناسه‌های یکتا دارد، StatefulSet مناسب است.

![image](https://github.com/milad6745/Kubernetes/assets/113288076/8aad3b13-97c0-4845-bec7-75ea2ec49452)

