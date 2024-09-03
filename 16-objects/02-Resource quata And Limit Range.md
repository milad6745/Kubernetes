در Kubernetes، `Resource Quota` و `Limit Range` دو ابزار مهم برای مدیریت منابع در سطح namespace و pod هستند که به شما کمک می‌کنند مصرف منابع را کنترل و مدیریت کنید.

### 1. **Resource Quota (سهمیه منابع)**
`Resource Quota` به شما امکان می‌دهد میزان منابعی که یک namespace می‌تواند مصرف کند را محدود کنید. با استفاده از Resource Quota می‌توانید اطمینان حاصل کنید که هیچ کدام از namespaceها بیش از حد منابع cluster را مصرف نمی‌کنند و به این ترتیب از مصرف منابع به طور نامتعادل جلوگیری می‌شود.

#### مثال:
فرض کنید شما یک cluster با چندین namespace دارید و می‌خواهید اطمینان حاصل کنید که یک namespace خاص بیشتر از 2 CPU و 4 GB RAM استفاده نکند. یک Resource Quota به این شکل تعریف می‌کنید:

```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: example-quota
  namespace: my-namespace
spec:
  hard:
    requests.cpu: "2"
    requests.memory: "4Gi"
    limits.cpu: "2"
    limits.memory: "4Gi"
```

این تنظیمات مشخص می‌کنند که تمامی podهای درون `my-namespace` در مجموع نمی‌توانند بیش از 2 CPU و 4 GB RAM درخواست یا استفاده کنند.

### 2. **Limit Range (محدوده محدودیت)**
`Limit Range` برای تعیین محدودیت‌های منابع (مثل CPU و RAM) در سطح pod یا container در یک namespace استفاده می‌شود. به کمک Limit Range می‌توانید مقدار حداقل (`requests`) و حداکثر (`limits`) منابعی که یک pod یا container می‌تواند درخواست یا مصرف کند را تعیین کنید.

#### مثال:
فرض کنید شما می‌خواهید محدودیت‌هایی را بر روی هر container درون یک namespace اعمال کنید تا مطمئن شوید هیچ container کمتر از 100m CPU و 200Mi RAM درخواست نکند و همچنین بیش از 500m CPU و 1Gi RAM مصرف نکند. یک Limit Range به این شکل تعریف می‌شود:

```yaml
apiVersion: v1
kind: LimitRange
metadata:
  name: example-limits
  namespace: my-namespace
spec:
  limits:
  - default:
      cpu: 500m
      memory: 1Gi
    defaultRequest:
      cpu: 100m
      memory: 200Mi
    type: Container
```

این Limit Range تضمین می‌کند که هر container حداقل 100m CPU و 200Mi RAM درخواست می‌کند و حداکثر 500m CPU و 1Gi RAM استفاده خواهد کرد.

### کاربردهای این تنظیمات:
- **Resource Quota** برای مدیریت منابع در سطح namespace استفاده می‌شود و از مصرف بیش از حد منابع توسط یک namespace جلوگیری می‌کند.
- **Limit Range** به مدیریت منابع در سطح pod یا container کمک می‌کند و باعث می‌شود هر pod یا container تنها به منابع مشخص شده دسترسی داشته باشد.

این دو ابزار با هم به شما امکان می‌دهند تا مصرف منابع را در سطح کلی cluster و در سطح جزئی‌تر، یعنی pod و container، به خوبی مدیریت کنید.s
