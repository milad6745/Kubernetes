در Kubernetes، **Pod Priority** و **Priority Class** ابزاری برای مدیریت اولویت پردازش و زمان‌بندی پادها (Pods) در یک کلاستر هستند. این ابزارها کمک می‌کنند که اگر منابع کلاستر محدود باشند (مانند CPU یا حافظه)، پادهایی با اولویت بالاتر بتوانند اجرا شوند و حتی در صورت نیاز پادهای با اولویت پایین‌تر حذف شوند تا منابع آزاد شوند.

---

### **Priority Class**
**Priority Class** یک منبع در Kubernetes است که اولویت‌های عددی مشخصی را برای پادها تعیین می‌کند. هر Priority Class یک مقدار عددی (`value`) دارد که نشان‌دهنده اهمیت آن است. مقادیر بالاتر نشان‌دهنده اولویت بالاتر هستند.

ویژگی‌های اصلی Priority Class:
1. **value**: عددی که اولویت را تعیین می‌کند.
2. **globalDefault**: مشخص می‌کند که این Priority Class به‌صورت پیش‌فرض برای پادهایی که Priority Class ندارند استفاده شود یا نه.
3. **description**: توضیحی در مورد این Priority Class.

---

### **Pod Priority**
وقتی یک Priority Class به یک پاد تخصیص داده می‌شود، Kubernetes مقدار آن را به پاد اعمال می‌کند و بر اساس آن تصمیم‌گیری می‌کند که کدام پادها اجرا یا حذف شوند.

---

### **مثال کاربردی**
فرض کنید دو نوع برنامه در کلاستر شما اجرا می‌شوند:
1. برنامه‌های حیاتی (critical).
2. برنامه‌های با اولویت کمتر (non-critical).

#### 1. تعریف Priority Class
ابتدا یک Priority Class برای هر دسته تعریف می‌کنیم.

```yaml
apiVersion: scheduling.k8s.io/v1
kind: PriorityClass
metadata:
  name: critical-apps
value: 1000
globalDefault: false
description: "Priority for critical applications"
---
apiVersion: scheduling.k8s.io/v1
kind: PriorityClass
metadata:
  name: low-priority
value: 100
globalDefault: false
description: "Priority for non-critical applications"
```

#### 2. استفاده در پادها
پادهایی که از این Priority Class استفاده می‌کنند، باید در مشخصات خود به آن اشاره کنند:

پاد حیاتی:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: critical-pod
spec:
  containers:
  - name: nginx
    image: nginx
  priorityClassName: critical-apps
```

پاد با اولویت کم:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: low-priority-pod
spec:
  containers:
  - name: nginx
    image: nginx
  priorityClassName: low-priority
```

---

### **عملکرد در شرایط بحرانی**
اگر منابع کلاستر محدود شوند:

   - پادهای با Priority پایین‌تر (مانند `low-priority-pod`) ممکن است حذف شوند تا منابع برای پادهای با Priority بالاتر (مانند `critical-pod`) آزاد شوند.

اگر پادهای جدید با Priority بالا نیاز به اجرا داشته باشند، ممکن است Kubernetes پادهای کم‌اولویت‌تر را برای فراهم کردن منابع Terminate کند.

---

### **نکات مهم**
- مقدار Priority Class می‌تواند مثبت یا منفی باشد.
- Priority Class

- پیش‌فرض (`globalDefault: true`) به تمام پادهایی که Priority Class مشخص ندارند اعمال می‌شود.
- مدیریت صحیح Priority Class برای حفظ پایداری سرویس‌های حیاتی در کلاستر بسیار اهمیت دارد. 

اگر نیاز به مثال‌های پیچیده‌تر یا توضیحات بیشتر دارید، اطلاع دهید!
