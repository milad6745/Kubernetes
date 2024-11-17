برای ایجاد یک **Rollout** در Kubernetes، شما معمولاً از یک **Deployment** استفاده می‌کنید. Deployment ها به شما این امکان را می‌دهند که نسخه‌های جدیدی از برنامه‌ها را به صورت تدریجی و ایمن روی Pods اعمال کنید. وقتی که نسخه جدیدی از برنامه یا تصویر (image) ایجاد می‌شود، Deployment به شما این امکان را می‌دهد که یک Rollout جدید شروع کنید و Kubernetes به طور خودکار Pods را بروزرسانی می‌کند.

در اینجا یک مثال از چگونگی ایجاد یک **Rollout** در Kubernetes با استفاده از یک **Deployment** آورده شده است:

### مثال: ایجاد یک Rollout با استفاده از Deployment در Kubernetes

1. **تعریف یک فایل YAML برای Deployment**
   ابتدا یک فایل YAML برای Deployment ایجاد می‌کنیم که شامل اطلاعاتی درباره نام برنامه، نسخه تصویر (image) و تعداد replicas است. فرض کنید می‌خواهیم یک برنامه وب ساده با تصویر `nginx` در نسخه `1.19.0` راه‌اندازی کنیم.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3  # تعداد پادهایی که می‌خواهیم
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
        image: nginx:1.19.0  # نسخه اولیه از تصویر Nginx
        ports:
        - containerPort: 80
```

در این فایل:
- **replicas**: تعداد Podهایی که برای برنامه ایجاد می‌شود. در این مثال، ۳ Pod.
- **image**: نسخه‌ای از تصویر کانتینر که باید اجرا شود (در اینجا `nginx:1.19.0`).
- **selector**: Kubernetes باید Pods را با این label پیدا کند تا بتواند آن‌ها را مدیریت کند.
- **template**: تعریف Podهایی که توسط Deployment ایجاد می‌شوند.

2. **ایجاد Deployment**

برای ایجاد Deployment از فایل YAML فوق، دستور زیر را اجرا می‌کنیم:

```bash
kubectl apply -f nginx-deployment.yaml
```

این دستور Deployment را بر اساس تعریف فایل YAML می‌سازد و Pods مربوطه را ایجاد می‌کند.

3. **مشاهده وضعیت Deployment**

پس از ایجاد Deployment، می‌توانید وضعیت آن را با استفاده از دستور زیر مشاهده کنید:

```bash
kubectl get deployments
```

این دستور اطلاعات مربوط به Deployment ایجاد شده را نمایش می‌دهد، از جمله تعداد replicas که در حال حاضر در حال اجرا است.

4. **انجام Rollout جدید (به‌روزرسانی نسخه)**

حالا فرض کنید می‌خواهید نسخه Nginx را به نسخه جدیدتری، مثلاً `1.21.0` به‌روزرسانی کنید. شما می‌توانید این کار را با استفاده از دستور `kubectl set image` انجام دهید. به این صورت:

```bash
kubectl set image deployment/my-nginx-deployment nginx=nginx:1.21.0
```

این دستور باعث می‌شود که تصویر کانتینر Nginx از نسخه `1.19.0` به نسخه `1.21.0` به‌روزرسانی شود. Kubernetes به‌طور خودکار یک **Rollout** جدید آغاز می‌کند و Pods قدیمی را به تدریج با نسخه جدید جایگزین می‌کند.

5. **مشاهده وضعیت Rollout**

برای مشاهده وضعیت **Rollout** و پیگیری پیشرفت آن، از دستور زیر استفاده کنید:

```bash
kubectl rollout status deployment/my-nginx-deployment
```

این دستور به شما می‌گوید که آیا **Rollout** با موفقیت انجام شده است یا خیر.

6. **بازگشت به نسخه قبلی (Rollback)**

اگر به هر دلیلی به نسخه قبلی نیاز داشتید (مثلاً نسخه جدید مشکلی ایجاد کرده است)، می‌توانید به راحتی Rollout را به نسخه قبلی بازگردانید. برای این کار از دستور زیر استفاده کنید:

```bash
kubectl rollout undo deployment/my-nginx-deployment
```

این دستور **Rollout** را به نسخه قبلی باز می‌گرداند.

### جمع‌بندی:
- **Rollout** در Kubernetes یک فرآیند تدریجی برای به‌روزرسانی Pods است.
- شما می‌توانید نسخه‌های جدید برنامه را با استفاده از `kubectl set image` به راحتی بروزرسانی کنید.
- با استفاده از `kubectl rollout status` می‌توانید پیشرفت عملیات بروزرسانی را پیگیری کنید.
- در صورت بروز مشکلات، می‌توانید از دستور `kubectl rollout undo` برای بازگشت به نسخه قبلی استفاده کنید.

اگر سوال دیگری دارید یا به جزئیات بیشتری نیاز دارید، خوشحال می‌شوم کمک کنم!



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
