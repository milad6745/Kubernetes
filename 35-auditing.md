**Auditing** در Kubernetes به فرآیند ردیابی و ثبت تمام رویدادهای مرتبط با API Server اشاره دارد. این ویژگی به شما امکان می‌دهد فعالیت‌های کاربران، سرویس‌ها و دیگر اجزای کلستر را ثبت و مانیتور کنید. Auditing برای اهداف زیر مفید است:

1. **مانیتورینگ امنیتی:** شناسایی فعالیت‌های غیرمجاز.
2. **دیباگ و عیب‌یابی:** ردیابی مشکلات در کلستر.
3. **رعایت مقررات:** ذخیره تاریخچه‌ای از فعالیت‌ها برای انطباق با استانداردهای قانونی.

---

### اجزای اصلی Auditing:

#### 1. **Audit Policy (سیاست‌های نظارت):**
فایل سیاست تعیین می‌کند که کدام رویدادها ثبت شوند و به چه شکل ذخیره شوند.
سیاست‌ها شامل چهار سطح هستند:
**None:**


 رویدادی ثبت نمی‌شود.
**Metadata:**


 فقط اطلاعات متادیتا (مانند زمان، کاربر) ثبت می‌شود.
**Request:**


 طلاعات متادیتا همراه با داده‌های درخواست ثبت می‌شود
  
**RequestResponse:**


     
اطلاعات متادیتا، داده‌های درخواست، و پاسخ ثبت می‌شود.


   **مثال یک فایل سیاست Audit:**
   ```yaml
   apiVersion: audit.k8s.io/v1
   kind: Policy
   rules:
   - level: Metadata
     users: ["system:authenticated"]
     verbs: ["get", "list", "watch"]
     resources:
     - group: ""
       resources: ["pods", "services"]
   - level: RequestResponse
     verbs: ["create", "delete"]
   ```

#### 2. **Audit Log (لاگ‌ها):**
   - خروجی رویدادها در فایل‌های لاگ ذخیره می‌شود.
   - مسیر پیش‌فرض لاگ‌ها معمولاً در API Server تنظیم می‌شود.

---

### پیکربندی Auditing:

1. **ایجاد یک فایل سیاست Audit:**
   - فایل YAML بالا را ذخیره کنید، مثلاً با نام `audit-policy.yaml`.

2. **تنظیمات API Server:**
   - فایل سیاست را به API Server معرفی کنید.
   - در فایل تنظیمات API Server، پارامترهای زیر را اضافه کنید:
     ```bash
     --audit-policy-file=/path/to/audit-policy.yaml
     --audit-log-path=/var/log/kubernetes/audit.log
     --audit-log-maxage=30
     --audit-log-maxbackup=10
     --audit-log-maxsize=100
     ```

   - سپس API Server را ری‌استارت کنید.

3. **بررسی لاگ‌ها:**
   - لاگ‌ها را می‌توانید با استفاده از ابزارهای مانیتورینگ یا به صورت دستی بررسی کنید:
     ```bash
     tail -f /var/log/kubernetes/audit.log
     ```

---

### ساختار یک رویداد در لاگ Audit:

هر رویداد در قالب JSON ذخیره می‌شود. یک نمونه:
```json
{
  "kind": "Event",
  "apiVersion": "audit.k8s.io/v1",
  "level": "Metadata",
  "timestamp": "2024-12-30T12:34:56Z",
  "user": {
    "username": "system:admin",
    "groups": ["system:authenticated"]
  },
  "verb": "create",
  "objectRef": {
    "resource": "pods",
    "namespace": "default",
    "name": "example-pod"
  },
  "responseStatus": {
    "metadata": {},
    "code": 201
  }
}
```

- **user:** کاربری که درخواست را ارسال کرده.
- **verb:** عملی که انجام شده (مثل `create` یا `delete`).
- **objectRef:** منبعی که تحت تأثیر قرار گرفته.
- **responseStatus:** نتیجه درخواست.

---

### ابزارهای پیشرفته برای Auditing:

1. **ELK Stack:** برای تجزیه و تحلیل لاگ‌ها.
2. **Splunk:** برای ذخیره و جستجوی لاگ‌ها.
3. **Fluentd:** برای ارسال لاگ‌ها به سیستم‌های خارجی.
