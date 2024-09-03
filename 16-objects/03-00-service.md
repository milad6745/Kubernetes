##service kubernetes
![image](https://github.com/user-attachments/assets/c4f3b793-065f-4329-918f-1906a02f10e1)

### سرویس (Service) در Kubernetes چیست؟

در Kubernetes، **Service** یک آبستراکشن است که مجموعه‌ای از پادها (Pods) را به عنوان یک سرویس شبکه‌ای یکپارچه در دسترس قرار می‌دهد. این سرویس‌ها ارتباطات شبکه‌ای پایدار و قابل اعتماد را بین پادها یا بین پادها و کاربران خارجی فراهم می‌کنند.

### دلایل استفاده از سرویس:
**دستیابی پایدار به پادها**:
  
  پادها به طور پویا ایجاد و حذف می‌شوند و IP آن‌ها ممکن است تغییر کند. سرویس‌ها با ارائه یک IP ثابت (ClusterIP) یا یک DNS نام‌گذاری شده، دسترسی پایدار به پادها را فراهم می‌کنند.
  
   **لود بالانسینگ (Load Balancing)**:
  
  سرویس‌ها می‌توانند ترافیک ورودی را بین پادهای مختلف توزیع کنند و از این طریق لود بالانسینگ انجام دهند.

**انتزاع شبکه‌ای**:
  
   سرویس‌ها یک سطح انتزاع شبکه‌ای فراهم می‌کنند، بنابراین مشتریان لازم نیست بدانند کدام پاد یا کدام IP باید درخواست‌های خود را ارسال کنند.

### انواع سرویس‌ها:
1. **ClusterIP (پیش‌فرض)**:
   این نوع سرویس یک IP داخلی درون کلاستر اختصاص می‌دهد که فقط از داخل کلاستر قابل دسترسی است. از آن برای ارتباطات داخلی بین پادها استفاده می‌شود.
  
2. **NodePort**:

 این سرویس یک پورت خاص روی هر Node باز می‌کند و از طریق آن می‌توان به سرویس از خارج از کلاستر دسترسی داشت.
  
3. **LoadBalancer**:

   این نوع سرویس از طریق Load Balancer خارجی (مانند AWS ELB یا Google Cloud Load Balancer) ترافیک را به سرویس هدایت می‌کند.
  
4. **ExternalName**:

این سرویس یک نام DNS خارجی را به یک سرویس داخلی نگاشت می‌کند.

### مثال:

در اینجا یک مثال ساده از یک سرویس `ClusterIP` که ترافیک را به یک پاد `nginx` هدایت می‌کند آورده شده است:

#### تعریف یک پاد Nginx:


```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  labels:
    app: nginx
spec:
  containers:
  - name: nginx
    image: nginx:latest
    ports:
    - containerPort: 80
```

#### تعریف سرویس برای دسترسی به پاد Nginx:


```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: ClusterIP
```

### توضیحات:
- **selector**: این قسمت تعیین می‌کند که سرویس باید ترافیک را به پادهایی که دارای لیبل `app: nginx` هستند هدایت کند.
- **ports**: پورت `80` از طریق سرویس در دسترس قرار می‌گیرد و به پورت `80` درون پاد هدایت می‌شود.
- **type: ClusterIP**: این نوع سرویس فقط از داخل کلاستر قابل دسترسی است.

### اجرای سرویس:
پس از تعریف پاد و سرویس، می‌توانید فایل YAML را با دستور زیر اجرا کنید:

```bash
kubectl apply -f nginx-pod.yaml
kubectl apply -f nginx-service.yaml
```

سپس می‌توانید سرویس را با دستور زیر بررسی کنید:

```yaml
kubectl get all
NAME                READY   STATUS             RESTARTS        AGE
pod/nginx-service   1/1     Running            0               34m


NAME                    TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
service/nginx-service   NodePort    10.96.211.178   <none>        80:30240/TCP   35m
```

```bash
kubectl get services
```

این دستور سرویس‌های فعال را نمایش می‌دهد و می‌توانید از طریق IP داخلی کلاستر به سرویس `nginx` دسترسی داشته باشید.