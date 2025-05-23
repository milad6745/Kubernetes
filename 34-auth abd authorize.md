
در Kubernetes، فرآیند **Authentication (احراز هویت)** و **Authorization (مجوزدهی)** برای کنترل دسترسی به منابع کلستر انجام می‌شود. این دو مرحله نقش اساسی در امنیت کلستر دارند. در ادامه هر کدام را توضیح می‌دهیم:

---

### 1. **Authentication (احراز هویت)**

این مرحله مشخص می‌کند که "شما چه کسی هستید؟" یا "کلاینتی که درخواست می‌دهد چه کسی است؟".

Kubernetes از چندین روش برای احراز هویت پشتیبانی می‌کند:

#### الف) **TLS Certificates (گواهی TLS):**
   - در زمان ایجاد یک کلاینت، یک گواهی TLS که توسط کلستر قابل اعتماد است صادر می‌شود.
   - کلاینت با استفاده از این گواهی به سرور Kubernetes (API Server) متصل می‌شود.

#### ب) **Token-based Authentication (توکن):**
   - برای کاربران و سرویس‌ها، Kubernetes از **توکن‌ها** (مثل ServiceAccount Token) استفاده می‌کند.
   - این توکن‌ها در هدر درخواست‌ها ارسال می‌شوند.
   - مثال:
     ```
     Authorization: Bearer <TOKEN>
     ```

#### ج) **Basic Authentication (کاربر و کلمه عبور):**
   - فایل `basic-auth.csv` روی API Server ذخیره می‌شود که شامل اطلاعات کاربران است.
   - این روش کمتر توصیه می‌شود و معمولاً برای توسعه یا تست استفاده می‌شود.

#### د) **OpenID Connect (OIDC):**
   - Kubernetes می‌تواند از سیستم‌های احراز هویت خارجی مثل Google, Okta, یا Keycloak پشتیبانی کند.
   - OIDC یک روش امن برای احراز هویت کاربران سازمانی است.

#### هـ) **Webhook Token Authentication:**
   - برای اعتبارسنجی توکن‌های سفارشی از یک وب‌سرور خارجی استفاده می‌شود.

#### و) **Static Token Files (توکن‌های استاتیک):**
   - فایل‌های استاتیکی که شامل لیستی از توکن‌ها و کاربران هستند.

---

### 2. **Authorization (مجوزدهی)**

بعد از احراز هویت، Kubernetes بررسی می‌کند که آیا کاربر اجازه انجام عملیات درخواستی را دارد یا خیر. این مرحله مشخص می‌کند که "شما چه کاری می‌توانید انجام دهید؟".

Kubernetes از چندین روش برای مجوزدهی پشتیبانی می‌کند:

#### الف) **Role-Based Access Control (RBAC):**
   - یک سیستم مبتنی بر نقش برای کنترل دسترسی به منابع.
   - **Role:** سطح دسترسی در یک **namespace** خاص.
   - **ClusterRole:** سطح دسترسی در کل کلستر.
   - **RoleBinding:** اختصاص یک Role به یک کاربر یا گروه در یک namespace.
   - **ClusterRoleBinding:** اختصاص یک ClusterRole به یک کاربر یا گروه در کل کلستر.

   **مثال Role:**
   ```yaml
   apiVersion: rbac.authorization.k8s.io/v1
   kind: Role
   metadata:
     namespace: default
     name: pod-reader
   rules:
   - apiGroups: [""]
     resources: ["pods"]
     verbs: ["get", "list", "watch"]
   ```

#### ب) **Node Authorization:**
   - برای کنترل دسترسی نودها به منابع API Server.

#### ج) **Webhook Authorization:**
   - درخواست‌ها به یک وب‌سرور خارجی ارسال می‌شوند تا مجوزدهی بررسی شود.

---

### فرآیند کلی:
1. **Client** (کاربر یا برنامه) درخواست خود را به API Server می‌فرستد.
2. **API Server** ابتدا کاربر را **احراز هویت** می‌کند.
3. سپس بررسی می‌کند که آیا این کاربر اجازه انجام عملیات درخواستی را دارد (**مجوزدهی**).
4. در صورت موفقیت‌آمیز بودن هر دو مرحله، درخواست اجرا می‌شود.

---

### ابزارهای مرتبط:
- برای مشاهده لاگ‌های احراز هویت و مجوزدهی:
  ```bash
  kubectl logs -n kube-system <kube-apiserver-pod>
  ```

- برای تست درخواست‌ها:
  ```bash
  kubectl auth can-i <verb> <resource> --as=<user>
  ```



🔐 چه کارهایی می‌تونی محدود یا اجازه بدی؟
با verbsهایی مثل:

get, list, watch (فقط خوندن)

create, update, patch, delete (نوشتن)

و منابعی مثل:

pods, services, deployments, configmaps, و...

