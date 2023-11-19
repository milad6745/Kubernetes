# Name Space

در Kubernetes، `Namespace` یک مفهوم است که به شما اجازه می‌دهد تا محیط‌های مختلف و جداگانه‌ای از یکدیگر ایجاد کنید و منابع Kubernetes را درون آن‌ها گروه‌بندی کنید. از این طریق می‌توانید یک محیط چندتاپی (multi-tenancy) ایجاد کنید، به‌طوری‌که منابع یک `Namespace` از دیگر `Namespaces` مجزا باشند. این امکان مفید است زمانی که شما یک کلاستر Kubernetes را با چند تیم یا چند برنامه مختلف به اشتراک گذاشته و نیاز به جداسازی منابع دارید.

برخی از کاربردهای معمول `Namespaces` عبارتند از:

**جداسازی تیم‌ها یا پروژه‌ها:** با ایجاد `Namespace`ها، می‌توانید منابع مرتبط با یک تیم یا پروژه را در یک فضای نام جداگانه قرار دهید.

**تست و توسعه:** می‌توانید `Namespace`ها را برای جداسازی محیط‌های توسعه، آزمایش و تولید (development, staging, production) استفاده کنید.

**مدیریت دسترسی (RBAC):** از `Namespaces` برای اعمال سیاست‌های دسترسی (مانند نقش‌ها و مجوزها) در یک سطح `Namespace` خاص استفاده می‌شود.


```bash
kubectl create namespace mynamespace
```

سپس می‌توانید منابع خود را درون این `Namespace` ایجاد کرده و مدیریت کنید.

## name space default on kubernetes

در Kubernetes، علاوه بر `default` که `Namespace` پیش‌فرض است، چندین `Namespace` دیگر نیز از پیش ساخته شده‌اند. این `Namespace`ها اغلب برای مقاصد خاصی طراحی شده‌اند و برخی از آنها به طور معمول در نصب استاندارد Kubernetes وجود دارند. در Kubernetes نسخه 1.22، برخی از `Namespace`های از پیش تعریف شده عبارتند از:

**`default`:**

این `Namespace` به‌طور پیش‌فرض برای ایجاد و مدیریت منابع بدون تعیین `Namespace` استفاده می‌شود.

**`kube-system`:** 

این `Namespace` برای اجزای سیستمی مانند `kube-dns`, `kube-proxy`, `kube-controller-manager`, و `kube-scheduler` استفاده می‌شود.

**`kube-public`:** 

منابع موجود در این `Namespace` به‌صورت عمومی قابل مشاهده هستند و برای اطلاعات عمومی و منابعی که می‌خواهید به تمام کاربران دسترسی داشته باشد، استفاده می‌شود.

**`kube-node-lease`:** 

این `Namespace` برای اطلاعاتی مانند lease های ترکیب کننده های مسیر (kubelet) استفاده می‌شود.

**`ingress-nginx`:**

این `Namespace` برای نصب مهمانی‌های Ingress Nginx مورد استفاده قرار می‌گیرد. این مهمانی‌ها برای مدیریت ترافیک و مسیریابی درون کلاستر استفاده می‌شوند.



## Name space reserve
نیم اسپیس های kube- رزرو خو کوبرنتیز است و نباید از آنها استفاده کرد.


## view namespace

```bash
# kubectl get ns
NAME                   STATUS   AGE
default                Active   7d5h
kube-node-lease        Active   7d5h
kube-public            Active   7d5h
kube-system            Active   7d5h
kubernetes-dashboard   Active   4d5h

# kubectl get pod -n default
NAME        READY   STATUS    RESTARTS   AGE
mc1         2/2     Running   0          56m
mypod       1/1     Running   0          125m
nginx       1/1     Running   0          6h16m
nginx-pod   1/1     Running   0          5d3h
```
## resource quata to namespace
میخواهیم منابع یک name space را محدود کنیم .
```bash
apiVersion: v1
kind: ResourceQuota
metadata:
  name: myresourcequota
  namespace: default
spec:
  hard:
    pods: "10"          # تعداد حداکثر Pods
    requests.cpu: "2"   # مقدار CPU حداکثر بر حسب میلی‌کور
    requests.memory: 4Gi # مقدار حداکثر حافظه بر حسب گیگابایت
    limits.cpu: "4"     # مقدار حداکثر CPU بر حسب میلی‌کور
    limits.memory: 8Gi  # مقدار حداکثر حافظه بر حسب گیگابایت
```
با این دستور منابع namespace default را محدود میکنیم .
برای بررسی دستور زیر را میزنیم :
```bash
#kubectl get resourcequotas
NAME              AGE   REQUEST                                                 LIMIT
myresourcequota   11s   pods: 4/10, requests.cpu: 0/2, requests.memory: 0/4Gi   limits.cpu: 0/4, limits.memory: 0/8Gi
```
