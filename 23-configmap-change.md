آهان، فهمیدم 😎
می‌خوای **nginx deployment + ConfigMap** داشته باشی و وقتی ConfigMap رو تغییر می‌دی، بدون هیچ downtime، Podها تنظیمات جدید رو بگیرن.
بیایم قدم‌به‌قدم بررسی کنیم:

---

# 1️⃣ مشکل اصلی

* Deployment با ConfigMap:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deploy
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
        image: nginx
        volumeMounts:
        - name: config-volume
          mountPath: /etc/nginx/conf.d
      volumes:
      - name: config-volume
        configMap:
          name: nginx-config
```

* وقتی ConfigMap تغییر می‌کنه:

  * Podهای قبلی **ConfigMap رو نمی‌بینند**
  * Pod جدید می‌سازه ConfigMap جدید
  * بدون set کردن چیزی → Podهای فعلی **هیچ تغییری نمی‌بینند**

---

# 2️⃣ راه‌حل‌های بدون downtime

## گزینه 1: Rolling Update با annotation (رایج‌ترین)

* Deployment را طوری تغییر می‌دهیم که **Podها ریستارت شوند**
* از annotation استفاده می‌کنیم:

```yaml
spec:
  template:
    metadata:
      annotations:
        configmap-reload-timestamp: "2026-02-05T18:00:00"
```

هر بار که ConfigMap عوض شد → این annotation رو آپدیت می‌کنیم
Kubernetes می‌بینه `template` تغییر کرده → **Rolling Update انجام می‌دهد**

* Podهای قدیمی به ترتیب terminate می‌شوند
* Podهای جدید با ConfigMap جدید شروع می‌شوند
* بدون downtime (چون replicas > 1)

---

## گزینه 2: استفاده از **sidecar برای reload**

* نصب **ConfigMap Reload Container** کنار nginx
* وظیفه‌اش: وقتی فایل‌ها در volume تغییر کرد → nginx را reload می‌کنه بدون اینکه Pod restart شود

### مثال با sidecar:

```yaml
containers:
- name: nginx
  image: nginx
  volumeMounts:
  - name: config-volume
    mountPath: /etc/nginx/conf.d

- name: config-reloader
  image: jimmidyson/configmap-reload
  args:
  - --volume-dir=/etc/nginx/conf.d
  - --webhook-method=POST
  - --webhook-url=http://localhost:8080/-/reload
  volumeMounts:
  - name: config-volume
    mountPath: /etc/nginx/conf.d
```

* این container هر تغییر در ConfigMap مونت شده را پایش می‌کند
* وقتی تغییر داد → nginx reload می‌شود (graceful)
* هیچ Pod restart نمی‌شود
* downtime = ۰ ✅

---

## گزینه 3: استفاده از **nginx-ingress + ConfigMap**

* اگر nginx داخل ingress controller باشه
* ingress controller خودش می‌تونه **hot reload** ConfigMap رو انجام بده
* برای deployment معمولی → گزینه 2 بهتره

---

# 🔑 نکات طلایی

1. **Rolling Update + annotation** → ساده، بدون sidecar، ولی Pod ریستارت می‌شود (با replicas کافی → downtime = 0)
2. **Sidecar configmap-reload** → حرفه‌ای، Pod restart نمی‌شود، true zero downtime
3. **Nginx باید graceful reload کنه**:

```bash
nginx -s reload
```

Sidecar همین کارو انجام می‌ده.

---

# جمع‌بندی پیشنهادی

* اگر replicas ≥ 2:
  **Rolling Update با annotation** ساده‌ترین راه است.
* اگر می‌خوای Podها **هیچوقت restart نشوند**:
  **ConfigMap Reload sidecar** را اضافه کن.

---

اگر بخوای، می‌تونم برات **یه manifest کامل nginx + ConfigMap Reload sidecar** بنویسم که کاملاً آماده باشه و وقتی ConfigMap تغییر کرد، nginx اتوماتیک reload شه بدون هیچ downtime 👌

می‌خوای برات بسازم؟
