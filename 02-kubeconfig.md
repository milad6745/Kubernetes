# Kubeconfig

Kubeconfig

یک فایل پیکربندی (configuration file) است که اطلاعاتی از جمله تنظیمات محیطی (environment settings)، اطلاعات احراز هویت (authentication information)، و تنظیمات دسترسی (access settings) را برای ارتباط با یک خوشه Kubernetes فراهم می‌کند.
این فایل به‌طور معمول برای دسترسی به یک کلاستر Kubernetes از طریق Command-Line Interface (CLI) یا ابزارهای مدیریتی مانند kubectl استفاده می‌شود.

فرض کنید شما می‌خواهید با یک خوشه Kubernetes ارتباط برقرار کنید. در این صورت، اطلاعات مورد نیاز مانند IP یا نام سرور، تنظیمات احراز هویت (مانند token یا certificate) و اطلاعات مربوط به تنظیمات دسترسی (context) به کمک Kubeconfig تعریف می‌شود.

یک Kubeconfig فایل JSON یا YAML است که شامل اطلاعات زیر می‌شود:

1. **Cluster Information (اطلاعات خوشه):**
   - نام یا آدرس سرور Kubernetes.
   - اطلاعات CA Certificate برای احراز هویت سرور.

2. **User Information (اطلاعات کاربر):**
   - نوع احراز هویت کاربر (مثل token یا certificate).
   - اطلاعات احراز هویت نظیر token یا مسیر certificate.

3. **Context Information (اطلاعات Context):**
   - نام context که ارتباط بین یک کاربر و یک خوشه را مشخص می‌کند.
   - نام خوشه.
   - نام کاربر.

یک فایل Kubeconfig ممکن است شامل چندین context باشد که به کمک دستور `kubectl config use-context` می‌توانید بین آن‌ها جابه‌جا شوید.

کار با فایل Kubeconfig به کمک دستورات `kubectl` یا ابزارهای مدیریتی دیگر Kubernetes امکان پذیر است و به افرادی که مدیریت چندین خوشه یا محیط Kubernetes را بر عهده دارند این امکان را می‌دهد تا به‌راحتی بین محیط‌ها و خوشه‌ها جابه‌جا شوند.

مسیر این فایل بصورت cat \$HOME/.kube/config میباشد .

## عوض کردن مسیر kubeconfig
مسیر این فایل بصورت پیشفرض cat $HOME/.kube/config میباشد .

برای تغییر مسیر فایل `kubeconfig` در `kubectl` یا سایر ابزارهای Kubernetes، شما می‌توانید از متغیر محیطی `KUBECONFIG` استفاده کنید یا مستقیماً مسیر فایل را تنظیم کنید. در ادامه، روش‌هایی برای انجام این کار توضیح داده شده‌اند:

### ۱. استفاده از متغیر محیطی `KUBECONFIG`:

شما می‌توانید متغیر محیطی `KUBECONFIG` را تنظیم کنید تا به مسیر فایل `kubeconfig` جدید اشاره کند. این متغیر به `kubectl` و سایر ابزارهای Kubernetes می‌گوید که از این فایل برای تنظیمات استفاده کنند.

```bash
export KUBECONFIG=/path/to/new/kubeconfig
```

### ۲. تنظیم مسیر در دستورات `kubectl`:

در صورتی که فقط برای یک دستور `kubectl` می‌خواهید از یک `kubeconfig` خاص استفاده کنید، می‌توانید مسیر را به صورت مستقیم در دستور اعلام کنید:

```bash
kubectl --kubeconfig=/path/to/new/kubeconfig get pods
```

### ۳. استفاده از چندین فایل `kubeconfig`:

اگر شما چندین فایل `kubeconfig` دارید و می‌خواهید از همه آن‌ها به‌طور همزمان استفاده کنید، می‌توانید از یک خط فرمان جداگانه برای هرکدام استفاده کنید:

```bash
kubectl --kubeconfig=/path/to/kubeconfig1 get pods
kubectl --kubeconfig=/path/to/kubeconfig2 get pods
```

با این کار، شما می‌توانید به سادگی میان چندین فایل `kubeconfig` جابه‌جا شوید یا از همه آن‌ها در یک دستور `kubectl` استفاده کنید.

توجه داشته باشید که این تغییرات معمولاً تاثیری بر روی تمام دستورات `kubectl` و ابزارهای Kubernetes دارد.

# view config
```
kubectl config get-contexts
CURRENT   NAME         CLUSTER      AUTHINFO     NAMESPACE
*         kind-kind    kind-kind    kind-kind
          kind-milad   kind-milad   kind-milad

kubectl config view
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://127.0.0.1:37399
  name: kind-kind
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://127.0.0.1:33943
  name: kind-milad
contexts:
- context:
    cluster: kind-kind
    user: kind-kind
  name: kind-kind
- context:
    cluster: kind-milad
    user: kind-milad
  name: kind-milad
current-context: kind-kind
kind: Config
preferences: {}
users:
- name: ahmad
  user:
    token: REDACTED
- name: kind-kind
  user:
    client-certificate-data: REDACTED
    client-key-data: REDACTED
- name: kind-milad
  user:
    client-certificate-data: REDACTED
    client-key-data: REDACTED

```
