# network and default policy

Network Policy

در Kubernetes یک منبع (resource) است که امکان کنترل دقیق‌تر ترافیک شبکه در داخل خوشه Kubernetes را فراهم می‌کند. این قابلیت به شما این اختیار را می‌دهد که تعیین کنید چه پادها می‌توانند با چه پادها یا خدمات دیگر درون خوشه ارتباط برقرار کنند و چه پروتکل‌ها و پورت‌ها مجاز هستند.

برخی از ویژگی‌ها و اهداف Network Policy عبارتند از:

1. **کنترل دسترسی:**
   - تعیین کنید چه پادها به چه پادها یا خدماتی می‌توانند متصل شوند.

2. **قابلیت جداسازی:**
   - جلوگیری از ارتباط بین برنامه‌ها و خدماتی که نباید با یکدیگر ارتباط داشته باشند.

3. **امنیت لایه شبکه:**
   - محدود کردن ترافیک به پورت‌ها و پروتکل‌های مشخص به منظور بهبود امنیت شبکه.

4. **تنظیم قوانین فایروال:**
   - شبیه به یک فایروال، امکان تعیین قوانین کنترل ترافیک را فراهم می‌کند.

5. **پیاده‌سازی اصطلاحات Zero Trust:**
   - اجرای اصطلاحات Zero Trust به معنای عدم اعتماد به صورت پیش‌فرض و تایید هر ارتباط بر اساس قوانین.

نمونه‌ای از یک Network Policy در Kubernetes می‌تواند به صورت زیر باشد:

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-nginx
spec:
  podSelector:
    matchLabels:
      app: nginx
  ingress:
  - from:
    - podSelector:
        matchLabels:
          role: frontend
    ports:
    - protocol: TCP
      port: 80
```

در این مثال:

- `podSelector` تعیین می‌کند که این قاعده برای پادهایی با برچسب `app=nginx` اعمال شود.
- `ingress` نشان‌دهنده قوانین ورود به پادها است.
- در اینجا، مشخص شده است که پادهایی که برچسب `role=frontend` دارند می‌توانند به پورت 80 پادهای با برچسب `app=nginx` دسترسی پیدا کنند.

Network Policy توسط محیط‌های Kubernetes مختلف پشتیبانی می‌شود و می‌تواند به عنوان یک ابزار مهم برای بهبود امنیت و کنترل ترافیک درون خوشه مورد استفاده قرار گیرد.


# Example 1 )
دو تا پاد در یک namespace بالا آورریم و سپس ارتباطاتشان را به هم چک کنیم :

```
apiVersion: v1
kind: Pod
metadata:
  name: pod-1
  namespace: default
spec:
  containers:
  - name: container-1
    image:  nginx:latest
---
apiVersion: v1
kind: Pod
metadata:
  name: pod-2
  namespace: default
spec:
  containers:
  - name: container-2
    image: nginx:latest
```

```
kubectl get pod -o wide
NAME                          READY   STATUS    RESTARTS   AGE    IP            NODE                           NOMINATED NODE   READINESS GATES
pod-1                         1/1     Running   0          6s     10.244.0.35   delete-cluster-control-plane   <none>           <none>
pod-2                         1/1     Running   0          6s     10.244.0.34   delete-cluster-control-plane   <none>           <none>
```

به داخل پاد 1 میرویم و پینگ   10.244.0.34 را بررسی میکنیم .

```
kubectl exec -it pod-1  ping 10.244.0.34
64 bytes from 10.244.0.34: icmp_seq=1 ttl=63 time=0.108 ms
```

حالا با ایجاد یک network policy دسترسی بیسن این دو پاد را قطع میکنیم .

```
# network-policy.yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: deny-pod-access
  namespace: your-namespace
spec:
  podSelector:
    matchLabels:
      app: pod-1
  policyTypes:
  - Egress
  egress:
  - to:
    - podSelector:
        matchLabels:
          app: pod-2
```

