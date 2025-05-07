
---

### ✅ مراحل نصب Metrics Server:

1. **دانلود فایل YAML نصب رسمی:**

```bash
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
```

> این دستور فایل‌های لازم رو مستقیم از GitHub نصب می‌کنه.

---

2. **بررسی وضعیت Pod مربوطه:**

```bash
kubectl get pods -n kube-system
```

باید یک پاد به اسم شبیه `metrics-server-xxxxxxxxxx-xxxxx` ببینی که در حال اجرا باشه (`STATUS: Running`).

---

3. **اگر پاد بالا نیومد یا CrashLoop خورد:**

ممکنه لازم باشه آرگومان زیر رو به deployment اضافه کنی:

* ویرایش فایل deployment:

```bash
kubectl edit deployment metrics-server -n kube-system
```

و داخل قسمت `containers.args` این خط رو اضافه کن:

```yaml
- --kubelet-insecure-tls
```

> این گزینه TLS verification رو غیرفعال می‌کنه، چون در بعضی موارد (مخصوصاً در کلاسترهای تستی یا لوکال) ارتباط با kubelet با TLS معتبر ممکن نیست.

---

4. **تست کارکرد Metrics Server:**

بعد از نصب موفق:

```bash
kubectl top nodes
kubectl top pods
```




ممنون، این خروجی نشون می‌ده که کانتینر Metrics Server **راه افتاده ولی آماده نیست**، چون `readiness probe` با کد HTTP 500 برمی‌گرده. این معمولاً به خاطر TLS verification هست.

---

### ✅ اگر هنوز `--kubelet-insecure-tls` اضافه نکردی، حتماً این کار رو انجام بده:

#### 1. ویرایش دیپلویمنت:

```bash
kubectl edit deployment metrics-server -n kube-system
```

#### 2. توی بخش `spec.template.spec.containers[0].args` این خط رو اضافه کن:

```yaml
- --kubelet-insecure-tls
```

اگر از قبل آرگومان‌هایی مثل `--cert-dir` یا `--secure-port` وجود داره، فقط این خط رو به لیست اضافه کن.

---

### 🔄 بعد از ویرایش:

پس از ذخیره فایل:

```bash
kubectl rollout restart deployment metrics-server -n kube-system
```

سپس بررسی کن:

```bash
kubectl get pods -n kube-system
```

و در نهایت:

```bash
kubectl top nodes
NAME     CPU(cores)   CPU%   MEMORY(bytes)   MEMORY%
master   1165m        3%     9204Mi          28%
worker   1906m        47%    2354Mi          40%

```
