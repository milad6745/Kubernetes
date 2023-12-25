# volume

ما نباید دیتا داخل پادمان ذخیره کنیم . باید دیتایمان را به بیرون از کانتینر بکشیم .

# host path volume
در Kubernetes، `hostPath` یک نوع volume است که به شما امکان می‌دهد یک دایرکتوری یا فایل مستقر در سیستم میزبان (سرور کلود) را به عنوان یک Volume به داخل یک پاد متصل کنید. این به شما این امکان را می‌دهد که اطلاعات بین میزبان و پاد به اشتراک گذاشته شوند.

از `hostPath` معمولاً در محیط‌های توسعه و آزمایشی استفاده می‌شود، اما باید با احتیاط و با آگاهی از امنیت استفاده شود، زیرا این مکانیزم ممکن است باعث ایجاد وابستگی نسبت به سیستم میزبان شود و مشکلات امنیتی احتمالی را به همراه آورد.

برای مثال، اگر شما یک دایرکتوری `/data` روی میزبان دارید و می‌خواهید آن را به عنوان یک Volume در یک پاد Kubernetes متصل کنید، از `hostPath` می‌توانید به صورت زیر استفاده کنید:


```yaml
apiVersion: v1
kind: Pod
metadata:
  name: example-pod
spec:
  containers:
  - name: example-container
    image: nginx
    volumeMounts:
    - name: data-volume
      mountPath: /usr/share/nginx/html
  volumes:
  - name: data-volume
    hostPath:
      path: /data
```

در این مثال:

- `/data` یک دایرکتوری روی میزبان است که به عنوان `hostPath` مشخص شده است.
- `example-container` یک کانتینر است که این Volume را در مسیر `/usr/share/nginx/html` مونت می‌کند.
- این کانتینر از تصویر `nginx` استفاده می‌کند، و اطلاعات در دایرکتوری `/data` از میزبان به کانتینر انتقال می‌یابند.

توجه داشته باشید که استفاده از `hostPath` ممکن است مشکلات امنیتی را به وجود آورده و باید با احتیاط و آگاهی از موارد مرتبط با امنیت استفاده شود.

# Empty Dir

EmptyDir یک نوع Volume در Kubernetes است که به صورت موقتی در یک پاد ایجاد می‌شود. EmptyDir برای اشتراک دادن اطلاعات بین میانهای مختلف یک پاد مورد استفاده قرار می‌گیرد.

ویژگی‌های EmptyDir:

1. **موقتی:** اطلاعات در EmptyDir تا زمانی که پاد فعال است باقی می‌ماند. اگر پاد حذف شود یا متوقف شود، اطلاعات موجود در EmptyDir نیز حذف می‌شود.

2. **مشترک بین کانتینرها:** اگر یک پاد شامل چند کانتینر باشد، همه آن‌ها می‌توانند از یک EmptyDir مشترک استفاده کنند.

برای استفاده از EmptyDir، باید یک Volume در مانیفست پاد تعریف کرده و آن را به کانتینر مربوطه متصل کنید. در زیر یک نمونه از استفاده از EmptyDir آورده شده است:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: example-pod
spec:
  containers:
  - name: container-1
    image: nginx
    volumeMounts:
    - name: shared-data
      mountPath: /usr/share/nginx/html
  - name: container-2
    image: busybox
    command: ["/bin/sh", "-c", "echo Hello from the second container > /shared-data/index.html"]
    volumeMounts:
    - name: shared-data
      mountPath: /shared-data
  volumes:
  - name: shared-data
    emptyDir: {}
```

در این مثال:

- `container-1` از تصویر `nginx` استفاده می‌کند و EmptyDir را در مسیر `/usr/share/nginx/html` مونت می‌کند.
- `container-2` از تصویر `busybox` استفاده می‌کند و یک فایل به نام `index.html` در دایرکتوری `/shared-data` ایجاد می‌کند که در واقع در EmptyDir قرار دارد.
- هر دو کانتینر از یک EmptyDir به نام `shared-data` استفاده مشترک می‌کنند.

از این EmptyDir می‌توان در مواردی مانند اشتراک اطلاعات موقتی بین کانتینرهای یک پاد یا انجام وظایف مشابه استفاده کرد.
