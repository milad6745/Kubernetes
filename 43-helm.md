**Helm** 

یک ابزار مدیریت بسته برای Kubernetes است که کار مدیریت، انتشار، و به‌روزرسانی منابع Kubernetes را ساده می‌کند. Helm مشابه مدیر بسته‌هایی مانند **apt** در اوبونتو یا **yum** در لینوکس عمل می‌کند، اما به‌طور خاص برای Kubernetes طراحی شده است.

---

### ویژگی‌های اصلی Helm
1. **Charts:**
 
 بسته‌هایی که با Helm مدیریت می‌شوند، به نام **Charts** شناخته می‌شوند.
 یک Chart شامل همه فایل‌های مورد نیاز برای اجرای یک برنامه در Kubernetes است، از جمله:
فایل‌های تعریف منابع (مثل Deployment، Service)
 متغیرهای پیکربندی

2. **Repository:**
   
   - Helm از مخازن (Repository) برای ذخیره و اشتراک‌گذاری Charts استفاده می‌کند.
 مخازن معروف: [ArtifactHub](https://artifacthub.io)

5. **Release Management:**
 وقتی یک Chart روی کلاستر نصب می‌شود، به آن یک **Release** گفته می‌شود.
   - Helm امکان مدیریت و به‌روزرسانی Releaseها را به‌سادگی فراهم می‌کند.

---

### مزایای استفاده از Helm
1. **سادگی در مدیریت برنامه‌ها:**

   - به‌جای نوشتن چندین فایل YAML، یک Chart همه تنظیمات را در خود جای می‌دهد.
   
2. **قابلیت پیکربندی بالا:**

   - Charts قابل تنظیم هستند و می‌توانید مقادیر (Values) خاصی را برای نیازهای خود جایگزین کنید.

3. **به‌روزرسانی آسان:**
   - با استفاده از Helm می‌توانید نسخه جدید یک Chart را به‌راحتی به‌روزرسانی کنید.

4. **Rollback:**
   - Helm به شما اجازه می‌دهد به نسخه‌های قبلی یک Release برگردید.

5. **نصب سریع برنامه‌های پیچیده:**
   - برنامه‌های چندبخشی با منابع مختلف (مانند WordPress یا Elasticsearch) می‌توانند با یک دستور ساده نصب شوند.

---

### دستورات پرکاربرد Helm
1. **نصب Helm:**
   - نصب Helm روی سیستم:
     ```bash
     curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
     ```

2. **جستجوی یک Chart در مخازن:**
   ```bash
   helm search repo <chart-name>
   ```

3. **نصب یک Chart:**
   ```bash
   helm install <release-name> <chart-name>
   ```

4. **لیست Releaseها:**
   ```bash
   helm list
   ```

5. **به‌روزرسانی یک Release:**
   ```bash
   helm upgrade <release-name> <chart-name>
   ```

6. **حذف یک Release:**
   ```bash
   helm uninstall <release-name>
   ```

---

### مثال:
نصب NGINX از طریق Helm:

```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
helm install my-nginx bitnami/nginx
```

این دستورات:
- مخزن **bitnami** را اضافه می‌کنند.
- NGINX را از این مخزن نصب می‌کنند.

---

### ساختار یک Chart
یک Chart معمولاً شامل فایل‌های زیر است:
```
my-chart/
  Chart.yaml       # اطلاعات درباره Chart (نام، نسخه)
  values.yaml      # متغیرهای پیش‌فرض پیکربندی
  templates/       # فایل‌های YAML منابع Kubernetes
```

اگر سوال بیشتری در مورد Helm دارید، بپرسید!
