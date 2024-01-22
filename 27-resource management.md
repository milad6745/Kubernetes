##`Resource Management`
در این شکل نشان میدهد که ما نبیاد تمامی منابعمان را به پاد ها اختصاص دهیم .
مقداری برای سیستم عامل - مقداری برای kubelete و مقداری برای پاد ها و مقداری هم برای Eviction

![image](https://github.com/milad6745/Kubernetes/assets/113288076/28e51eaa-4377-4713-9f7e-0f6848126ac5)

در این شکل هم نشان میدهد که اگر تقریبن 64 کور CPU داریم 63 کور به پاد ها و کمتر از یک کور را  برای موارد دیگر در نطر بگیریم.

![image](https://github.com/milad6745/Kubernetes/assets/113288076/f05c49ed-77b2-4301-9565-388674c3d4ae)

در کل در یک نگاه میزان منابعی که بر میدارد برای غیر از استفاده پاد ها خیلی زیاد نیست
![image](https://github.com/milad6745/Kubernetes/assets/113288076/5ca81fee-aea7-4bdd-bbbb-926092a324ec)



تمامی کانفیگ ها با این چند خط در Kuebelete انجام میشود .

`Node has 32Gi of memory, 16 CPUs and 100Gi of Storage`
```
--kube-reserved is set to cpu=1,memory=2Gi, ephemeral-storage=1Gi
--system-reserved is set to cpu=500m,memory=1Gi, ephemeral-storage=1Gi
--eviction-hard is set to memory.available<500Mi, nodefs.available<10%
```

###`Set limit range fordefault namespace`
```
apiVersion: v1
kind: LimitRange
metadata:
  name: my-limit-range
  namespace: default
spec:
  limits:
  - type: Container
    max:
      memory: 256Mi
      cpu: 500m
    min:
      memory: 64Mi
      cpu: 100m
  - type: PersistentVolumeClaim
    max:
      storage: 1Gi
  - type: PersistentVolumeClaim
    min:
      storage: 100Mi
```

### `Check resource POD`

برای چک کردن میزان منابع (ریسورس‌ها) تعیین شده برای یک پاد در Kubernetes، می‌توانید از دو روش استفاده کنید: استفاده از دستورهای `kubectl` و نیز نگاه به توصیفی YAML (manifest) مربوط به پاد.

### استفاده از دستورهای `kubectl`:

1. **استفاده از `kubectl describe pod`:**
   این دستور اطلاعات جزئی و جامعی از پاد را نشان می‌دهد، از جمله میزان منابع مصرفی توسط کانتینرها. به عنوان مثال:

   ```bash
   kubectl describe pod <نام-پاد>
   ```

   این دستور اطلاعات جزئی در مورد پاد را نمایش می‌دهد، از جمله نیازمندی‌ها و تخصیص‌های منابع.

2. **استفاده از `kubectl top pod`:**
   این دستور مشخصات مصرف منابع (مانند CPU و حافظه) را برای پادهای درحال اجرا نشان می‌دهد. برای نمونه:

```bash

kubectl top pod <نام-پاد>
NAME                               CPU(cores)   MEMORY(bytes)
example-deployment-5c78b98-cvgfz   1m           7Mi
example-pod                        0m           7Mi
example-pod-emptydir               0m           7Mi
example-pod1                       0m           7Mi
hello-world-8977c54c9-gmlnh        0m           0Mi
hello-world-8977c54c9-pg22t        0m           1Mi
my-pod                             0m           7Mi
```

   این دستور اطلاعات مربوط به مصرف منابع را به صورت زمان واقعی نشان می‌دهد.

### نگاه به YAML توصیف پاد:

1. **استفاده از `kubectl get pod -o yaml`:**
   این دستور یک نمایه YAML از وضعیت کنونی پادها را نشان می‌دهد. با این دستور، می‌توانید به صورت مستقیم به توصیف پاد دسترسی پیدا کنید و بررسی کنید که چه میزان از منابع تخصیص یافته است. به عنوان مثال:

   ```bash
   kubectl get pod <نام-پاد> -o yaml
   ```

   سپس در YAML، به بخش‌های `resources.requests` و `resources.limits` نگاه کنید تا میزان منابع مصرفی و تخصیص یافته به کانتینرها را بررسی کنید.


### `reserve kubelet resource`

### استفاده از خط فرمان:

همچنین، می‌توانید پارامترها را به صورت مستقیم در خط فرمان اجرای kubelet انتقال دهید:

```bash
kubelet --kube-reserved=memory=1Gi --kube-reserved=cpu=0.5
```

یا به صورت پارامترهای زیر:

```bash
kubelet --kube-reserved=memory=1Gi,cpu=0.5
```

با این تنظیمات، میزان مشخصی از حافظه و پردازنده برای kubelet رزرو می‌شود و از این منابع توسط kubelet استفاده نخواهد شد.

توجه داشته باشید که استفاده از این تنظیمات نیاز به درک دقیق از نیازها و شرایط محیطی شما دارد. مقادیر رزرو شده باید با نیازهای کلی کل کلوستر و سایر کارلرها (controllers) سازگار باشند.

برای هر دو روش، اطلاعات مصرف منابع در بخش‌هایی از خروجی نشان داده می‌شوند که با `resources.requests` و `resources.limits` شروع می‌شوند. این بخش‌ها نشان‌دهنده میزان حداقل (requests) و حداکثر (limits) منابع تخصیص داده شده به پاد هستند.
