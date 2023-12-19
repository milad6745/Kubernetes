# finalizer
Finalizers 

در Kubernetes کلیدهای فضای نام هستند که به Kubernetes می‌گویند که منتظر شود تا شرایط خاصی قبل از حذف کامل منابع مشخص شده برآورده شود. Finalizers کنترلرها را مطلع می‌کنند که باید منابعی که شیء حذف شده متعلق به آنهاست را پاک کنند.

زمانی که به Kubernetes می‌گویید یک شیء را که برای آن finalizer مشخص شده است حذف کند، API Kubernetes شیء را برای حذف نشانه‌گذاری می‌کند با پر کردن فیلد .metadata.deletionTimestamp و یک کد وضعیت 202 (HTTP "Accepted") به عنوان پاسخ برمی‌گرداند. شیء هدف در حالت حذفی باقی می‌ماند در حالی که صفحه کنترل یا سایر مؤلفه‌ها، اقداماتی که توسط finalizerها تعریف شده‌اند را انجام می‌دهند. پس از انجام این اقدامات، کنترلر finalizerهای مربوط را از شیء هدف حذف می‌کند. هنگامی که فیلد metadata.finalizers خالی است، Kubernetes حذف کامل را در نظر می‌گیرد و شیء را حذف می‌کند.

این آبجکت حفاظت میکند از اینکه دلیت اشتباه انجام شود .
```yaml
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: foobar-deployment
  finalizers:
  - example.com/test-finalizer
spec:
  replicas: 1
  template:
    metadata:
      labels:
        foo: bar
    spec:
      containers:
      - image: nginxdemos/hello
        name: main
        ports:
        - containerPort: 80
          protocol: TCP
```
![image](https://github.com/milad6745/Kubernetes/assets/113288076/6fdd6b2e-d5ed-461a-b919-9348a6d574b6)
![image](https://github.com/milad6745/Kubernetes/assets/113288076/ff958e67-a3a0-4eb9-8bcb-8fee87f1fb0c)

