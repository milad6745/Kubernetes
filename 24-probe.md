# `Probe`

در Kubernetes، Probe‌ها (تست‌ها) وظیفه ارزیابی وضعیت یک Container (ظاهراً یک نرم‌افزار یا سرویس در حاشیه) را برعهده دارند. این Probe‌ها به کاوش در وضعیت سلامت، آمادگی و یا اطلاعات دیگر مرتبط با یک Container می‌پردازند. توسط Probe‌ها، Kubernetes می‌تواند تصمیم‌گیری در مورد ادامه یا ایقاف عملکرد یک Container و یا حتی تغییرات در جریان کارهایی که با آن مرتبط هستند را انجام دهد.

سه نوع اصلی Probe در Kubernetes وجود دارند:

1. **Liveness Probe (تست زنده بودن):**

این Probe بررسی می‌کند که آیا Container هنوز زنده است یا خیر. اگر Liveness Probe نتیجه منفی دهد، Kubernetes ممکن است تصمیم بگیرد که مجدداً Container را راه‌اندازی کند.

2. **Readiness Probe (تست آمادگی):**
  
    این Probe بررسی می‌کند که آیا Container آماده برای درخواست‌های ترافیک و خدمت دهی به آن‌ها است یا خیر. اگر یک Container آماده نباشد (نتیجه منفی داشته باشد)، Kubernetes ممکن است آن Container را از توزیع درخواست‌ها خارج کند تا به آن زمان که آماده شود.


3. **Startup Probe (تست شروع):**
  
 این نوع Probe بررسی می‌کند که آیا Container در حالت شروع و اجرا قرار گرفته است یا خیر. اگر یک Container در حالت شروع قرار نگیرد (نتیجه منفی داشته باشد)، Kubernetes ممکن است دوباره تلاش کند تا زمانی که Container به درستی راه‌اندازی شود.

این Probe‌ها به تنظیمات Pod و Container افزوده می‌شوند و با کمک آنها، Kubernetes می‌تواند به صورت اتوماتیک وضعیت و سلامت سیستم‌ها را ارزیابی کرده و اقدامات مناسبی انجام دهد.


### `Example`
در این مثال به livenss , redyness در apiserver میپردازیم
 kubectl describe -n kube-system  pod kube-apiserver-delete-cluster-control-plane

  Liveness:     http-get https://172.18.0.3:6443/livez delay=10s timeout=15s period=10s #success=1 #failure=8
  Readiness:    http-get https://172.18.0.3:6443/readyz delay=0s timeout=15s period=1s #success=1 #failure=3


### 'create deploymet with liveness & rediness'
برای اجرای یک Deployment در Kubernetes که Probe‌های Liveness و Readiness دارد، شما باید یک فایل YAML تعریف کنید و سپس از آن استفاده کنید. در زیر یک نمونه Deployment با Probe‌های Liveness و Readiness آورده شده است:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: example-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: example
  template:
    metadata:
      labels:
        app: example
    spec:
      containers:
      - name: example-container
        image: your-container-image:tag
        ports:
        - containerPort: 80
        readinessProbe:
          httpGet:
            path: /healthz
            port: 80
          initialDelaySeconds: 5
          periodSeconds: 10
        livenessProbe:
          httpGet:
            path: /healthz
            port: 80
          initialDelaySeconds: 10
          periodSeconds: 20
```

در این مثال:

- `readinessProbe` تعیین می‌کند که Kubernetes باید هر 10 ثانیه یک بار به آدرس `/healthz` در پورت 80 ارسال درخواست HTTP کند و اگر درخواست موفق باشد (کد وضعیت 200)، Container آماده به درخواست‌هاست. این Probe بعد از 5 ثانیه از زمان راه‌اندازی Container فعال می‌شود.

- `livenessProbe` همچنین تعیین می‌کند که Kubernetes باید هر 20 ثانیه یک بار به آدرس `/healthz` در پورت 80 ارسال درخواست HTTP کند و اگر درخواست موفق نباشد (کد وضعیت غیر 200)، Container به عنوان زنده در نظر گرفته نخواهد شد و ممکن است Kubernetes تصمیم بگیرد که Container را دوباره راه‌اندازی کند. این Probe بعد از 10 ثانیه از زمان راه‌اندازی Container فعال می‌شود.

لطفاً مقادیر مربوط به `your-container-image:tag` را با اطلاعات مربوط به تصویر Container خود جایگزین کنید. همچنین می‌توانید مسیر `/healthz` و پورت را بر اساس نیازهای خود تغییر دهید.
