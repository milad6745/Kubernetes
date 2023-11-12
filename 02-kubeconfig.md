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
