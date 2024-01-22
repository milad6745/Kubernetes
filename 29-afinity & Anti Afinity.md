# Pod To Node


در Kubernetes، `Affinity` و `Anti-Affinity` مفاهیمی هستند که به شما این امکان را می‌دهند تا Pod‌ها را بر اساس ویژگی‌های خاصی به Node‌ها تخصیص دهید یا از تخصیص آنها به Node‌های خاص جلوگیری کنید.

### Affinity:

- **Node Affinity:**
- 
- توسط Node Affinity می‌توانید Pod‌ها را به Node‌ها بر اساس برچسب‌ها یا توپولوژی (Topology) مختلف (مانند رشته مکانی یا رشته داده) تخصیص دهید. این بدان معناست که می‌توانید تعیین کنید Pod‌ها تنها در Node‌هایی اجرا شوند که شرایط مشخصی را دارند.

  ```yaml
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: example-key
            operator: In
            values:
            - example-value
  ```

### Anti-Affinity:

- **Node Anti-Affinity:**
-
- توسط Node Anti-Affinity می‌توانید جلوگیری کنید تا Pod‌ها به Node‌هایی که شرایط خاصی را دارند، تخصیص یابند. به عبارت دیگر، می‌توانید از این ویژگی برای جلوگیری از اجرای یک یا چند Pod در یک Node مشخص استفاده کنید.

  ```yaml
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: example-key
            operator: NotIn
            values:
            - example-value
  ```

توجه داشته باشید که این مثال‌ها به Node Affinity و Anti-Affinity بر اساس برچسب‌ها اشاره دارند، اما می‌توان از موارد دیگر مانند توپولوژی نیز برای تعیین Affinity و Anti-Affinity استفاده کرد.
مثلا میتوانیم به اپلیکیشن امان بگوییم جایی اجرا شو که ردیس وجود دارد . یا جایی که فلان اپلیکیشم هست اجرا نشو .
![image](https://github.com/milad6745/Kubernetes/assets/113288076/38358739-db88-4a86-8888-ba42ff4e8f25)


استفاده از Affinity و Anti-Affinity به شما این امکان را می‌دهد که به صورت انعطاف‌پذیرتری تصمیم‌گیری کنید که Pod‌های شما در کدام Node‌ها اجرا شوند یا نشوند، و این امکان را به شما می‌دهد که برنامه‌هایی با معماری متنوع و تنوع زیادی را در یک کلاستر اجرا کنید.

## `inter pod afinity and anti afinity`
در Kubernetes، `Interpod Affinity` و `Interpod Anti-Affinity` به شما امکان می‌دهند تا تنظیماتی را بر روی Pod‌ها اعمال کنید تا مشخص کنید Pod‌هایی با هم یا دور از هم (با توجه به نیازهای شما) قرار گیرند. این تنظیمات می‌توانند بر اساس برچسب‌ها یا توپولوژی (Topology) مختلف تعیین شوند.

### Interpod Affinity:

- **Pod Affinity:** توسط Pod Affinity می‌توانید مشخص کنید که یک Pod معین باید با یک یا چند Pod دیگر در یک Node یا در Node‌های مختلف قرار گیرد. این به کمک توپولوژی‌های مختلف (مثل هم‌زمان یا پیاپی) و برچسب‌ها انجام می‌شود.

  ```yaml
  affinity:
    podAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
      - labelSelector:
          matchExpressions:
          - key: app
            operator: In
            values:
            - frontend
        topologyKey: kubernetes.io/hostname
  ```

### Interpod Anti-Affinity:

- **Pod Anti-Affinity:** توسط Pod Anti-Affinity می‌توانید جلوگیری کنید تا یک Pod با یک یا چند Pod دیگر در یک Node یا در Node‌های مختلف قرار گیرد. این نیز مشابه Pod Affinity عمل می‌کند، اما جلوگیری از اجتماع Pod‌ها را تعیین می‌کند.

  ```yaml
  affinity:
    podAntiAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
      - labelSelector:
          matchExpressions:
          - key: app
            operator: In
            values:
            - frontend
        topologyKey: kubernetes.io/hostname
  ```

در این مثال‌ها، `topologyKey` تعیین می‌کند که به کمک کدام ویژگی (برچسب یا توپولوژی) قرار است این تطابق انجام شود. `requiredDuringSchedulingIgnoredDuringExecution` نیز مشخص می‌کند که این تطابق در زمان زمانبندی Pod‌ها لازم است و در طول اجرا نباید نادیده گرفته شود.

استفاده از Interpod Affinity و Anti-Affinity به شما این امکان را می‌دهد که تنظیمات مختلفی را بر روی Pod‌ها اعمال کنید تا مدیریت دقیق‌تری بر روی توزیع Pod‌ها در کلاستر داشته باشید. این امکان به شما کمک می‌کند تا اطمینان حاصل کنید که Pod‌های مهم در کنار یا دور از یکدیگر قرار می‌گیرند و یا برعکس، Pod‌های نادرست یا تداخل‌آفرین در نزدیکی یکدیگر نمی‌افتند.



### `Example`

برای اجرای یک محیط که شامل Redis، Nginx، و یک برنامه Node.js با node affinity در یک پاد Kubernetes استفاده می‌شود، شما به دنبال ایجاد یک فایل YAML برای تعریف منابع Kubernetes خود هستید. در ادامه یک نمونه YAML برای این منظور آورده شده است:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-app-pod
spec:
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: app
            operator: In
            values:
            - my-node-selector-value
  containers:
    - name: redis-container
      image: redis:latest
    - name: nginx-container
      image: nginx:latest
    - name: nodejs-container
      image: your-nodejs-image:latest
```

لطفاً توجه داشته باشید که:

1. `my-app-pod` نام پاد شما است. شما می‌توانید این نام را به دلخواه تغییر دهید.
2. `my-node-selector-value` یک مقدار برچسب nodeSelector است که به عنوان معیار برای انتخاب یک نود برای اجرای پاد مورد استفاده قرار می‌گیرد. این مقدار باید با مقدار تنظیم شده برای برچسب nodeSelector بر روی نودهای خود شما هماهنگ باشد.

پس از ایجاد فایل YAML خود، می‌توانید آن را با دستور `kubectl apply -f your-file.yaml` اجرا کنید تا پاد ایجاد شود و برنامه‌ها در نود مورد نظر اجرا شوند.


## `Anti afinity Example`

استفاده از Anti-Affinity در Kubernetes به شما این امکان را می‌دهد که جلوگیری کنید تا پادهای مشابه یا اجزای یک برنامه روی یک نود واحد قرار گیرند. این کار می‌تواند به توزیع بار و افزایش قابلیت اطمینان کمک کند.

در ادامه، یک نمونه از یک فایل YAML برای تعریف پاد با Anti-Affinity آورده شده است:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-app-pod
spec:
  affinity:
    podAntiAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        - labelSelector:
            matchExpressions:
              - key: app
                operator: In
                values:
                  - my-app
          topologyKey: "kubernetes.io/hostname"
  containers:
    - name: redis-container
      image: redis:latest
    - name: nginx-container
      image: nginx:latest
    - name: nodejs-container
      image: your-nodejs-image:latest
  labels:
    app: my-app
```

لطفاً توجه داشته باشید که:

1. `my-app-pod` نام پاد شما است. شما می‌توانید این نام را به دلخواه تغییر دهید.
2. مقدار `app: my-app` برچسبی است که بر روی تمام پادهای مرتبط با برنامه شما تنظیم شده است.
3. `topologyKey: "kubernetes.io/hostname"` اینجا نشان‌دهنده این است که Anti-Affinity بر اساس نام هاست انجام شود.

این Anti-Affinity rule می‌گوید که پادهایی که دارای برچسب `app: my-app` هستند، باید در هنگام اجرا (و هنگام برنامه) روی نودهای مختلف قرار گیرند. این کار می‌تواند به بهبود قابلیت اطمینان سیستم و جلوگیری از تمرکز افزایش یافته روی یک نود کمک کند.
