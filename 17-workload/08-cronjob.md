# cronjob

`CronJob`

در Kubernetes یک workload  است که برای اجرای وظایف برنامه‌ریزی شده و تکراری در زمان‌های خاص استفاده می‌شود. `CronJob` از ساختار زمان‌بندی شبیه به `cron` استفاده می‌کند که به کاربر اجازه می‌دهد وظایف را در زمان‌ها و تاریخ‌های خاصی اجرا کند.

ویژگی‌های `CronJob` عبارتند از:

**زمان‌بندی مبتنی بر `cron`:**

   می‌توانید زمان اجرای `CronJob` را با استفاده از فرمت `cron` (مانند "0 2 * * *" برای هر روز ساعت 2 صبح) تنظیم کنید.

**مدیریت بازه زمانی (Concurrency Policy):**

   `CronJob` اجازه می‌دهد تا چندین اینستانس از وظیفه (Job) در یک زمان خاص اجرا شوند یا مطمئن می‌شود که فقط یک اینستانس از وظیفه در هر زمان اجرا می‌شود.

**مدیریت شگفت‌انگی (Retries):**

   `CronJob` می‌تواند تنظیمات مدیریت خطا را انجام دهد و در صورتی که Job با موفقیت اجرا نشود، تا تعداد مشخصی تکرار شود.

**پیش‌نمایش زمان بازدید (Deadline):**

   می‌توانید مهلت زمانی مشخصی برای اجرای هر Job تعیین کنید.

**توسعه‌پذیری افقی:**

   می‌توانید تعداد replica (کپی) از Job را مشخص کنید تا در صورت نیاز به اجرای همزمان بیشتر، این Job متعددی ایجاد شود.

مثال یک `CronJob` ساده:

```yaml
apiVersion: batch/v1beta1
kind: CronJob
metadata:
  name: example-cronjob
spec:
  schedule: "0 2 * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: example-container
            image: busybox
            command: ["echo", "Hello from the cron job!"]
  concurrencyPolicy: Forbid
```

در این مثال:

- `example-cronjob`: نام `CronJob`.
- `schedule`: زمان‌بندی مبتنی بر `cron` (در اینجا هر روز ساعت 2 صبح).
- `example-container`: یک کانتینر ساده با تصویر `busybox`.
- `command`: دستوری که در این کانتینر اجرا می‌شود (در اینجا فقط یک دستور echo).
- `concurrencyPolicy`: مدیریت بازه زمانی؛ در این مثال تنظیم شده است که تنها یک اینستانس از Job در هر زمان اجرا شود.

شما می‌توانید این `CronJob` را با دستور `kubectl apply -f your-cronjob-file.yaml` اجرا کنید.
