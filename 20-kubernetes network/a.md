بسیار خب! در اینجا یک نمونه مانیفست در کوبرنتیز آورده شده که یک پاد و یک سرویس ایجاد می‌کند:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
  - name: my-container
    image: nginx

---
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  selector:
    app: my-pod
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
  type: ClusterIP
```

این مانیفست دو بخش دارد:
1. **پاد (Pod):** این بخش یک پاد با یک کانتینر ایجاد می‌کند که تصویر آن `nginx` است.
2. **سرویس (Service):** این سرویس به پاد ایجاد شده دسترسی می‌دهد. سرویس با استفاده از یک `selector` پادهای مرتبط را پیدا می‌کند و به آن‌ها روی پورت 80 دسترسی می‌دهد.

این مانیفست را می‌توانید با استفاده از `kubectl apply -f <file-name>.yaml` اعمال کنید تا منابع ایجاد شوند. اگر نیاز به تغییرات بیشتر دارید یا سوالی دارید، من اینجا هستم تا کمک کنم!
