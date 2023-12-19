برای اجرای یک پاد جدید در Kubernetes، از دستور `kubectl run` استفاده می‌شود. این دستور به شما این امکان را می‌دهد تا یک پاد با یک کانتینر ایجاد کرده و آن را در کلاستر اجرا کنید. در ادامه یک مثال ساده آورده شده است:

1. **ایجاد یک پاد:**
2. 
   ```bash
   kubectl run nginx-pod --image=nginx --port=80
   ```
   در اینجا:
   
   - `mypod`: نامی که به پاد اختصاص داده می‌شود.
   - `--image=nginx`: ایمیجی که برای اجرای کانتینر در پاد استفاده می‌شود (در اینجا از Nginx استفاده شده است).
   - `--port=80`: پورتی که برای کانتینر مشخص شده است.

3. **بررسی وضعیت پاد:**
```bash
   kubectl get pods
```
   این دستور وضعیت تمام پادهای در حال اجرا را نشان می‌دهد.

4. **بررسی وضعیت پاد با جزئیات بیشتر:**
   
```bash
   kubectl describe pod mypod

   kubectl describe pod nginx-pod
Name:             nginx-pod
Namespace:        default
Priority:         0
Service Account:  default
Node:             milad-cluster-control-plane/172.18.0.2
Start Time:       Tue, 14 Nov 2023 18:54:42 +0300
Labels:           run=nginx-pod
Annotations:      <none>
Status:           Running
IP:               10.244.0.11
IPs:
  IP:  10.244.0.11
Containers:
  nginx-pod:
    Container ID:   containerd://e0e5c404217847b9f2e31734515ad0b91155bbec2edefc48a7340819065d93e3
    Image:          nginx:latest
    Image ID:       docker.io/library/nginx@sha256:86e53c4c16a6a276b204b0fd3a8143d86547c967dc8258b3d47c3a21bb68d3c6
    Port:           80/TCP
    Host Port:      0/TCP
    State:          Running
      Started:      Tue, 14 Nov 2023 18:54:45 +0300
    Ready:          True
    Restart Count:  0
   ```
   این دستور اطلاعات جزئی‌تری در مورد پاد فراهم می‌کند.

4. **حذف پاد:**
   ```bash
   kubectl delete pod mypod
   ```
   این دستور پاد را از کلاستر حذف می‌کند.

توجه داشته باشید که استفاده از `kubectl run` به تنهایی یک روش ساده برای ایجاد پادها است. برای استفاده پیشرفته‌تر و مدیریت بهتر، به تعریف فایل‌های YAML یا استفاده از ابزارهای مانند Deployment ها متوسل شوید.
