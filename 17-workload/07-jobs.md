# jobs
در Kubernetes، `Job` یک workload است که برای اجرای یک کار یک بار (One-Time Task) و مطمئن شدن از اجرای موفقیت‌آمیز آن در کانتینرها (Pods) استفاده می‌شود. به عبارت دیگر، `Job` معمولاً برای اجرای یک فرآیند یک باری، مانند اجرای یک محاسبه، انجام یک پشتیبان‌گیری، یا هر وظیفه دیگری که نیاز به اجرای یکباری و موفقیت‌آمیز دارد، استفاده می‌شود.

ویژگی‌های `Job` عبارتند از:

**اجرا یکباری:**

برای اجرای یک فرآیند یک باری طراحی شده است. بعد از اتمام با موفقیت یک باره به پایان می‌رسد.

**ضمانت اجرا موفق:**

مطمئن می‌شود که تمام Pod ها که به عنوان بخشی از آن اجرا می‌شوند، با موفقیت اجرا شده‌اند. اگر کدی از یک Pod خروجی خطایی داشته باشد، `Job` مسئول متوقف کردن تمامی Pods و اطلاع به کاربر در مورد مشکل می‌شود.

**مدیریت Pod ها:**

تعداد مشخصی از Pod ها را برای اجرا ایجاد می‌کند و پس از اتمام وظیفه، این Pod ها را حذف می‌کند. تعداد Pod ها می‌تواند توسط کاربر مشخص شود.

**زمانبندی:**

می‌تواند به طور زمانی (CronJob) یا تنظیم شده بر اساس یک زمان خاص، اجرا شود.


مثال یک `Job` ساده:

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: example-job
spec:
  template:
    metadata:
      name: example-job
    spec:
      containers:
      - name: exjob
        image: busybox
        imagePullPolicy: Never
        command: ["echo", "Hello from the job!"]

      restartPolicy: OnFailure

```

در این مثال:

- `example-job`: نام `Job`.
- `example-container`: یک کانتینر ساده با تصویر `busybox`.
- `command`: دستوری که در این کانتینر اجرا می‌شود (در اینجا فقط یک دستور echo).
- `backoffLimit`: تعداد مجاز تلاش‌های مجدد در صورتی که `Job` نتواسته با موفقیت اجرا شود. در این مثال، تا دو بار مجدداً تلاش خواهد شد.

```
kubectl get pod                                                                                                                                      
NAME                          READY   STATUS              RESTARTS   AGE
pi-ftbcm                      0/1     ContainerCreating   0          18s
```
پس از اجرا کامل شده و از حالت اجرا خارج میشود .
```
kubectl get pod                                                                                                                                      
NAME                          READY   STATUS      RESTARTS   AGE
pi-ftbcm                      0/1     Completed   0          2m4s
```
```
kubectl get jobs.batch pi
NAME   COMPLETIONS   DURATION   AGE
pi     1/1           118s       24h
```

```
kubectl describe  jobs.batch
Name:             example-job
Namespace:        default
Selector:         batch.kubernetes.io/controller-uid=9384b68c-0e96-4aa6-b435-27c71c32b4da
Labels:           batch.kubernetes.io/controller-uid=9384b68c-0e96-4aa6-b435-27c71c32b4da
                  batch.kubernetes.io/job-name=example-job
                  controller-uid=9384b68c-0e96-4aa6-b435-27c71c32b4da
                  job-name=example-job
Annotations:      <none>
Parallelism:      1
Completions:      1
Completion Mode:  NonIndexed
Suspend:          false
Backoff Limit:    6
Start Time:       Sun, 13 Apr 2025 15:58:35 +0330
Completed At:     Sun, 13 Apr 2025 15:58:39 +0330
Duration:         4s
Pods Statuses:    0 Active (0 Ready) / 1 Succeeded / 0 Failed
Pod Template:
  Labels:  batch.kubernetes.io/controller-uid=9384b68c-0e96-4aa6-b435-27c71c32b4da
           batch.kubernetes.io/job-name=example-job
           controller-uid=9384b68c-0e96-4aa6-b435-27c71c32b4da
           job-name=example-job
  Containers:
   exjob:
    Image:      busybox
    Port:       <none>
    Host Port:  <none>
    Command:
      echo
      Hello from the job!
    Environment:   <none>
    Mounts:        <none>
  Volumes:         <none>
  Node-Selectors:  <none>
  Tolerations:     <none>
Events:
  Type    Reason            Age    From            Message
  ----    ------            ----   ----            -------
  Normal  SuccessfulCreate  2m29s  job-controller  Created pod: example-job-hxpnj
  Normal  Completed         2m24s  job-controller  Job completed
```
