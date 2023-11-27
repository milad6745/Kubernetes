# cronjob

`CronJob`

در Kubernetes یک workload  است که برای اجرای وظایف برنامه‌ریزی شده و تکراری در زمان‌های خاص استفاده می‌شود. `CronJob` از ساختار زمان‌بندی شبیه به `cron` استفاده می‌کند که به کاربر اجازه می‌دهد وظایف را در زمان‌ها و تاریخ‌های خاصی اجرا کند.

ویژگی‌های `CronJob` عبارتند از:

**زمان‌بندی مبتنی بر `cron`:**

   می‌توانید زمان اجرای `CronJob` را با استفاده از فرمت `cron` (مانند "0 2 * * *" برای هر روز ساعت 2 صبح) تنظیم کنید.

**مدیریت بازه زمانی (Concurrency Policy):**

   `CronJob` اجازه می‌دهد تا چندین اینستانس از وظیفه (Job) در یک زمان خاص اجرا شوند یا مطمئن می‌شود که فقط یک اینستانس از وظیفه در هر زمان اجرا می‌شود.



**پیش‌نمایش زمان بازدید (Deadline):**

   می‌توانید مهلت زمانی مشخصی برای اجرای هر Job تعیین کنید.

**توسعه‌پذیری افقی:**

   می‌توانید تعداد replica (کپی) از Job را مشخص کنید تا در صورت نیاز به اجرای همزمان بیشتر، این Job متعددی ایجاد شود.

مثال یک `CronJob` ساده:

```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: cronjob
spec:
  # https://crontab.guru/
  schedule: "* * * * *"
  concurrencyPolicy: Allow
  failedJobsHistoryLimit: 10
  successfulJobsHistoryLimit: 10
  jobTemplate:
    spec:
      template:
        spec:
          containers:
            - name: cronjob
              image: busybox:1.28
              args:
                - /bin/sh
                - -c
                - date; echo Hello from the Kubernetes cluster
          restartPolicy: OnFailure
```
مدام جاب تکرار میشه:
```yaml
kubectl get jobs.batch
NAME               COMPLETIONS   DURATION   AGE
cronjob-28351833   1/1           13s        91s
cronjob-28351834   1/1           4s         31s

kubectl get cronjobs.batch cronjob
NAME      SCHEDULE    SUSPEND   ACTIVE   LAST SCHEDULE   AGE
cronjob   * * * * *   False     0        36s             117s
```
