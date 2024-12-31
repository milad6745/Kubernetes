## role rolebinding
رول میگه چه دسترسی ای رول بایندینگ میگه چه کسی
که در اینجا گفته شده کاربر جین فقط بتواند get watch list را انجام دهد


![image](https://github.com/user-attachments/assets/afb8fb30-7f71-4e88-9835-20b9a30984b1)

این یک تعریف کامل از **Role** و **RoleBinding** در Kubernetes است که دسترسی کاربر "jane" را به **pods** در namespace `default` تنظیم می‌کند. در ادامه توضیح هر بخش آمده است:

---

### 1. **Role**

#### تعریف:
- **Role**
-
یک مجموعه قوانین است که تعیین می‌کند چه کاربرانی می‌توانند چه عملیاتی را روی چه منابعی انجام دهند.
در اینجا، Role به نام `pod-reader` اجازه **get**، **watch** و **list** روی منابع `pods` در namespace `default` می‌دهد.

#### جزئیات کد:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: default
  name: pod-reader
rules:
- apiGroups: [""] # "" indicates the core API group
  resources: ["pods"]
  verbs: ["get", "watch", "list"]
```

- **apiGroups: [""]**: مشخص می‌کند که این Role به گروه اصلی API دسترسی دارد. منابع اصلی مثل `pods` در این گروه هستند.
- **resources: ["pods"]**: نوع منابعی که می‌توانند مدیریت شوند.
- **verbs: ["get", "watch", "list"]**: عملیات مجاز.

---

### 2. **RoleBinding**

#### تعریف:

- **RoleBinding**،
- 
یک Role را به کاربران، گروه‌ها یا ServiceAccounts اختصاص می‌دهد.
در اینجا، RoleBinding به نام `read-pods`، Role `pod-reader` را به کاربر `jane` در namespace `default` اختصاص می‌دهد.

#### جزئیات کد:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: read-pods
  namespace: default
subjects:
- kind: User
  name: jane # "name" is case sensitive
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role # this must be Role or ClusterRole
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```

**subjects**: 

کاربر یا کاربران و نوع آنها (User، Group، یا ServiceAccount).
**kind: User**:

نوع سوژه، که در اینجا کاربر است.
**name: jane**: 

نام کاربر. نام حساس به بزرگی و کوچکی حروف است.
**roleRef**: 

تعیین نقش مرتبط.
**kind: Role**: 

مشخص می‌کند که به یک Role در namespace اشاره می‌کند.
**name: pod-reader**:

نام Role که باید با نام تعریف شده مطابقت داشته باشد.

---

### نتیجه:

پس از اعمال این تنظیمات:
1. کاربر `jane` می‌تواند منابع `pods` را در namespace `default` مشاهده کند (get، watch، list).
2. این سطح دسترسی تنها به namespace `default` محدود است.

---

### اعمال تنظیمات:
برای اعمال این فایل، آن را به صورت زیر ذخیره کنید (مثلاً `role-rolebinding.yaml`) و با دستور زیر اجرا کنید:
```bash
kubectl apply -f role-rolebinding.yaml
```

---

### تست دسترسی:
برای بررسی اینکه آیا کاربر `jane` دسترسی مورد نظر را دارد:
```bash
kubectl auth can-i get pods --as=jane --namespace=default
```

اگر همه چیز درست تنظیم شده باشد، خروجی باید `yes` باشد. 😊
