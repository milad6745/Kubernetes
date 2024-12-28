در Kubernetes، **Affinity** (وابستگی) و **Anti-affinity** (ضد وابستگی) مکانیزم‌هایی هستند که به شما اجازه می‌دهند کنترل کنید پادها (Pods) چگونه بر روی نودها (Nodes) یا در کنار پادهای دیگر قرار بگیرند. این مفاهیم در بخش

**Pod Affinity/Anti-affinity** 

در تنظیمات پاد تعریف می‌شوند.

### **Affinity (وابستگی)**

این ویژگی مشخص می‌کند که یک پاد ترجیح می‌دهد در کنار یک پاد دیگر یا روی یک نود خاص قرار بگیرد. 

#### کاربرد:
- برای قرار دادن پادهای مرتبط در کنار هم برای بهبود کارایی شبکه.
- برای کاهش تأخیر زمانی بین پادهای وابسته.

#### مثال:
فرض کنید یک برنامه دارای دو سرویس Frontend و Backend است و شما می‌خواهید پادهای این دو سرویس در یک نود مشترک قرار بگیرند:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: frontend
spec:
  affinity:
    podAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        - labelSelector:
            matchLabels:
              app: backend
          topologyKey: "kubernetes.io/hostname"
  containers:
    - name: frontend-container
      image: nginx
```

در این مثال، پاد `frontend` تلاش می‌کند در همان نودی مستقر شود که پادی با برچسب `app: backend` وجود دارد.

---

### **Anti-affinity (ضد وابستگی)**
این ویژگی مشخص می‌کند که یک پاد نباید در کنار یک پاد دیگر قرار بگیرد یا نباید روی یک نود خاص مستقر شود.

#### کاربرد:
- برای جلوگیری از مستقر شدن پادهای حیاتی بر روی یک نود (برای توزیع بار و افزایش پایداری).
- برای جداسازی پادهایی که ممکن است با هم تداخل داشته باشند.

#### مثال:
فرض کنید شما نمی‌خواهید دو پاد با نام `frontend` در یک نود مشترک قرار بگیرند:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: frontend
spec:
  affinity:
    podAntiAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        - labelSelector:
            matchLabels:
              app: frontend
          topologyKey: "kubernetes.io/hostname"
  containers:
    - name: frontend-container
      image: nginx
```

در این مثال، پاد `frontend` اطمینان می‌دهد که در یک نود با سایر پادهای دارای برچسب `app: frontend` مستقر نشود.

---

### **نکات کلیدی**
- `requiredDuringSchedulingIgnoredDuringExecution`: به معنای اجباری بودن شرط در زمان برنامه‌ریزی.
- `preferredDuringSchedulingIgnoredDuringExecution`: به معنای ترجیحی بودن شرط؛ اگر امکان‌پذیر باشد رعایت می‌شود.
- `topologyKey`: مشخص می‌کند که وابستگی یا ضد وابستگی در چه سطحی اعمال شود (مانند `kubernetes.io/hostname` برای نود یا `failure-domain.beta.kubernetes.io/zone` برای منطقه).

### کاربرد واقعی
این ویژگی‌ها معمولاً برای:
1. بهینه‌سازی استفاده از منابع.
2. افزایش دسترس‌پذیری سیستم.
3. جداسازی منطقی یا فیزیکی اجزای سیستم.
استفاده می‌شوند.
