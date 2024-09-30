PodDisruptionBudget (PDB)

یک منبع Kubernetes است که به شما اجازه می‌دهد تا محدودیت‌ها و قواعدی را برای تغییرات در توزیع پادها (Pods) در یک نود یا یک منطقه (Zone) مشخص کنید. هدف این منبع، مدیریت کنترل شده‌تر توزیع پادها در زمان اجرای تغییرات (مثل نگهداری، بروزرسانی یا عیب‌یابی) است تا تأثیرات جانبی به حداقل برسد.

ویژگی‌های اصلی PDB عبارتند از:

1. **minAvailable:** حداقل تعداد پادهایی که باید در هنگام اجرای تغییرات در دسترس باشند.

2. **maxUnavailable:** حداکثر تعداد پادهایی که می‌توانند در هنگام اجرای تغییرات بیرون از دسترس باشند.

3. **selector:** یک Label Selector که پادهای مشمول این PDB را تعیین می‌کند.

با استفاده از PodDisruptionBudget، شما می‌توانید تنظیمات مشخصی را برای هر پاد در یک Deployment یا ReplicaSet تعیین کنید. این تنظیمات مشخص می‌کنند که حداکثر یا حداقل چند درصد از پادها می‌توانند به طور همزمان در دسترس یا خارج از دسترس باشند.

در Kubernetes، **PodDisruptionBudget** (PDB) تضمین می‌کند که تعداد مشخصی از **Pod**ها در حین وقفه‌ها (مثل ارتقاهای نود یا حذف‌های خودکار) همچنان در حال اجرا باقی بمانند. این مکانیزم به شما کمک می‌کند تا در شرایطی مانند **voluntary disruptions** (مانند آپدیت نودها) تعداد خاصی از **Pod**ها را در دسترس نگه دارید.

در اینجا یک مثال ساده از **PodDisruptionBudget** برای یک **Deployment** با 3 نسخه از Nginx آورده شده است که اطمینان حاصل می‌کند حداقل 2 **Pod** همیشه فعال بمانند.

### 1. تعریف یک Deployment:

ابتدا یک **Deployment** برای اجرای 3 نمونه از Nginx را تعریف می‌کنیم.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:latest
```

### 2. تعریف PodDisruptionBudget:

سپس **PodDisruptionBudget** را تعریف می‌کنیم که تضمین می‌کند حداقل 2 نسخه از **Pod**ها همیشه در حال اجرا باقی بمانند.

```yaml
apiVersion: policy/v1
kind: PodDisruptionBudget
metadata:
  name: nginx-pdb
spec:
  minAvailable: 2
  selector:
    matchLabels:
      app: nginx
```

### توضیحات:

- **minAvailable: 2**: این تنظیم به این معناست که حداقل 2 نسخه از **Pod**ها باید همیشه در حال اجرا باشند. اگر تعداد **Pod**های فعال به کمتر از 2 برسد، عملیات اختلال (مانند ارتقاء نود یا توقف‌های داوطلبانه) متوقف خواهد شد.
- **selector**: بر اساس **label**‌های `app: nginx` اعمال می‌شود و این **PodDisruptionBudget** فقط برای **Pod**هایی که با این برچسب هم‌خوانی دارند، اعمال می‌شود.

### نحوه استفاده:

1. ابتدا **Deployment** را اعمال کنید:

```bash
kubectl apply -f nginx-deployment.yaml
```

2. سپس **PodDisruptionBudget** را اعمال کنید:

```bash
kubectl apply -f nginx-pdb.yaml
```

### نتیجه:

با اعمال این تنظیمات، Kubernetes اطمینان می‌دهد که در حین وقفه‌های داوطلبانه مانند به‌روزرسانی نودها، همیشه حداقل 2 **Pod** از Nginx فعال باقی بمانند. اگر یک نود یا منابع Kubernetes نتوانند حداقل 2 **Pod** را فعال نگه دارند، اختلال (disruption) انجام نخواهد شد.
