# autoscaling

1- horizental pod autoscaler (scaleout) (HPA)

از همون پاد چند تا ایجاد میکنیم . بزرگ میکنیم تعداد پاد ها را - stateless استفاده میشود .


2- vertical pod autoscaler (scale up) (VPA)

اضافه کردن منایع بع پاد - چاق کردن پاد - برای statefullset ها بیشتر کاربرد دارد چون نمیتوانند تهدادشان را افزایش دهند .

3- auto scale

اگر نیاز شد تعداد پاد هات رو افزایش بده به ماک replication برسون



# HPA

Hypervisor-based Pod Autoscaler (HPA) 
در Kubernetes یک ابزار است که به صورت خودکار تعداد Replicas یک پاد (Pod) را تنظیم می‌کند تا بهینه‌ترین تعداد منابع مورد نیاز برای اجرای برنامه در محیط Kubernetes فراهم شود. Pod یکی از مفاهیم اصلی در Kubernetes است و نمایانگر یک یا چند container که به صورت مشترک منابعی را به اشتراک می‌گذارند می‌باشد.

HPA 

به عنوان یک کنترل‌گر (Controller) در Kubernetes عمل می‌کند و بر اساس معیارهای تنظیم شده، مثل نرخ استفاده از CPU یا رم، تعداد Replicas را تنظیم می‌کند. این به این معناست که وقتی نیاز به منابع بیشتری برای اجرای برنامه وجود دارد (بر اساس تنظیمات HPA)، تعداد Replicas افزایش می‌یابد، و وقتی که نیاز به منابع کمتری وجود دارد، تعداد Replicas کاهش می‌یابد.

برای تعیین یک HPA، معمولاً یک فایل YAML مشخصات مورد نیاز برای HPA تعریف می‌شود. در این فایل، شما می‌توانید پارامترهایی مانند مازاد CPU یا رم که منجر به اجرای یک Replica اضافی می‌شوند، را مشخص کنید. به عنوان مثال:

```yaml
apiVersion: autoscaling/v2beta2
kind: HorizontalPodAutoscaler
metadata:
  name: example-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: example-deployment
  minReplicas: 1
  maxReplicas: 5
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 50
```

```
kubectl get hpa
NAME          REFERENCE                       TARGETS         MINPODS   MAXPODS   REPLICAS   AGE
example-hpa   Deployment/example-deployment   <unknown>/50%   1         5         0          36s
```

در این مثال، HPA به Deployment با نام "example-deployment" اشاره دارد و تنظیمات مربوط به نرخ استفاده از CPU را دارد. HPA تلاش می‌کند تا میانگین استفاده از CPU را در حد 50% نگه دارد و تعداد Replicas را بین حداقل 1 و حداکثر 5 تنظیم می‌کند.

با این تنظیمات، Kubernetes به صورت خودکار تغییرات در تعداد Replicas را اعمال می‌کند تا منابع برنامه با توجه به نیازهای فعلی بهینه شوند.

## Metric Server

Metric Server 

در Kubernetes یک کامپوننت است که معیارهای مختلفی از منابع سیستمی، مانند CPU و حافظه، را جمع‌آوری و ارائه می‌دهد. این معیارها به سایر کامپوننت‌های Kubernetes مانند Horizontal Pod Autoscaler (HPA) کمک می‌کنند تا تصمیمات بهینه‌تری در مورد افزایش یا کاهش تعداد Replicas یک پاد (Pod) بگیرند.

HPA 

به عنوان یک کنترل‌گر (Controller) در Kubernetes عمل می‌کند و بر اساس معیارهای تنظیم شده، تعداد Replicas را تنظیم می‌کند. این معیارها ممکن است شامل نرخ استفاده از CPU و حافظه باشند. Metric Server نقش اصلی خود را در ارائه این معیارها برای سایر کامپوننت‌ها ایفا می‌کند.

زمانی که HPA فعال شده و به یک معیار مشخص از Metric Server نیاز دارد (مثل نرخ استفاده از CPU)، Metric Server اطلاعات مربوط به این معیارها را فراهم می‌کند. HPA سپس بر اساس این اطلاعات تصمیم می‌گیرد که آیا تعداد Replicas باید افزایش یابد یا کاهش یابد.

به عبارت دیگر، Metric Server به HPA اطلاعات لازم را فراهم می‌کند تا این ابزار بتواند بر اساس شرایط فعلی منابع سیستمی (مثل CPU)، تصمیمات مربوط به افزایش یا کاهش تعداد Replicas را اعمال کند. این تعامل بین Metric Server و HPA موجب بهبود قابلیت‌های خودکارسازی و بهینه‌سازی منابع در محیط Kubernetes می‌شود.
