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

استفاده از Affinity و Anti-Affinity به شما این امکان را می‌دهد که به صورت انعطاف‌پذیرتری تصمیم‌گیری کنید که Pod‌های شما در کدام Node‌ها اجرا شوند یا نشوند، و این امکان را به شما می‌دهد که برنامه‌هایی با معماری متنوع و تنوع زیادی را در یک کلاستر اجرا کنید.
