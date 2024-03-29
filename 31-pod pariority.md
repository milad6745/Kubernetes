# 'Pod pariority'

در Kubernetes، پیش‌نهاد کارکرد (Priority) برای پادها (Pods) به عنوان "Pod Priority" نامیده می‌شود. این ویژگی به شما امکان می‌دهد که اهمیت یک پاد را نسبت به پادهای دیگر در یک نود و یا حتی در سراسر خوشه تعیین کنید.

زمانی که از Pod Priority استفاده می‌کنید، می‌توانید به Kubernetes بگویید که پادهای با اهمیت بیشتر در برخورد با منابع محدود نود‌ها یا در شرایط خاص اولویت بیشتری داشته باشند.

برای تنظیم اولویت یک پاد، باید یک تنظیم با نام "priorityClassName" را به داخل مشخصه‌های Pod اضافه کنید. این تنظیم به عنوان یک نام کلاس اولویت عمل می‌کند و به Kubernetes می‌گوید که از چه تنظیمات اولویتی برای این پاد استفاده کند.

مثال:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-priority-pod
spec:
  containers:
  - name: my-container
    image: nginx:latest
  priorityClassName: high-priority
```

در این مثال، تنظیم "priorityClassName" با مقدار "high-priority" به پاد اضافه شده است. این نام کلاس اولویت باید از پیش توسط مدیران خوشه تعریف شده باشد. مدیران خوشه می‌توانند تنظیمات اولویت مختلفی را بر اساس نیازهای سازمانی خود تعریف کنند و به این تنظیمات در تنظیمات مدیریتی خود اشاره کنند.

توجه داشته باشید که این قابلیت در نسخه‌های خاصی از Kubernetes (مانند نسخه‌های 1.14 به بالا) فعال است و نیاز به تنظیم‌های خاص در کنترل‌کننده اولویت (PriorityController) دارد.


## `Premtion`

Preemption

در Kubernetes به معنای ترکیب و اختصاص مجدد منابع به پادها (Pods) با اولویت بالا می‌باشد. وقتی که منابع محدود شده‌اند و یک پاد با اولویت بالا به سراغ می‌آید که نتواند منابع مورد نیاز خود را بر روی نودی بدست آورد، سیستم Kubernetes با استفاده از Preemption تصمیم می‌گیرد که یک یا چند پاد با اولویت پایین‌تر را از نود خود حذف کند و منابع مورد نیاز پاد با اولویت بالا را در اختیار آن قرار دهد.

برخی نکات کلیدی در مورد Preemption:

1. **اولویت پاد:** هر پاد از یک اولویت مشخص برخوردار است که با استفاده از `Priority` و `PriorityClassName` در مشخصات پاد تنظیم می‌شود.

2. **Priority Preemption:**
  
  در مواردی که یک نود فاقد منابع لازم برای یک پاد با اولویت بالا است، Preemption شروع می‌شود تا منابع مورد نیاز فراهم شوند.

5. **ترتیب Preemption:** Preemption به ترتیب اولویت پادها انجام می‌شود. پادهای با اولویت پایین‌تر به اولویت پایین‌تر هدف خواهند شد.

6. **تنظیمات مدیریت Preemption:** می‌توان تنظیمات مختلفی برای مدیریت Preemption در فایل تنظیمات کنترل‌کننده Preemption (preemption-controller.yaml) تعیین کرد.

7. **سازگاری با QoS Classes:** Preemption در نظر گرفتن سطوح کیفیت خدمات (QoS) نیز مهم است. به طور کلی، پادهای کمترین QoS اولویت بیشتری در Preemption دارند.

برای اطمینان از فعال بودن Preemption در یک خوشه Kubernetes، باید اطمینان حاصل شود که کنترل‌کننده Preemption به درستی پیکربندی شده باشد و قابلیت تخصیص مجدد منابع در خوشه فعال باشد. Preemption یکی از ابزارهای مهم برای بهینه‌سازی استفاده از منابع در Kubernetes است، به خصوص در مواقعی که خوشه با منابع محدود کار می‌کند.

## `Pariority class`


 در Kubernetes، priority class یک ویژگی است که به شما امکان می‌دهد اولویت پادها (Pods) را مشخص کرده و از اهمیت آن‌ها نسبت به یکدیگر در تخصیص منابع (مانند CPU و حافظه) تصمیم گیری کنید.

در واقع، priority class یک تنظیم مرتبط با پاد است که می‌تواند از یکی از دو ویژگی `Priority` یا `PriorityClassName` استفاده کند.

1. **Priority:** یک عدد صحیح که اولویت پاد را مشخص می‌کند. اعداد کوچکتر اولویت بالاتری دارند. این عدد باید در بازه `-10` تا `10` باشد.

2. **PriorityClassName:** یک نام کلاس اولویت است که از تنظیمات تعریف شده توسط مدیران خوشه می‌آید.

مثال:

```yaml
apiVersion: scheduling.k8s.io/v1
kind: PriorityClass
metadata:
  name: high-priority
value: 1000000
globalDefault: false
description: "High Priority Class"
```

در این مثال، یک priority class با نام "high-priority" تعریف شده است که از `Priority` با مقدار `1000000` استفاده می‌کند. این priority class می‌تواند به پادها اختصاص داده شود و پادهایی که از این کلاس استفاده می‌کنند، در تخصیص منابع اولویت بیشتری دارند.


### `priority class`
```
kubectl get priorityclasses
NAME                      VALUE        GLOBAL-DEFAULT   AGE
high-priority             1000000      false            3s
```

### `default pariority class`

```
kubectl get priorityclasses
NAME                      VALUE        GLOBAL-DEFAULT   AGE
system-cluster-critical   2000000000   false            57d
system-node-critical      2000001000   false            57d
```
