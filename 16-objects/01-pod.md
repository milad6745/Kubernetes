# pod
کوچکترین آبجکتی است که در کوبرنتیز قابل managed است 
![image](https://github.com/milad6745/Kubernetes/assets/113288076/31688089-600e-4b48-8f5a-bba2a220f8fe)
اصولا داخل هر پاد یک کانتینر اجرا میکنیم اما مدل های دیگر هم وجود دارد .

![image](https://github.com/milad6745/Kubernetes/assets/113288076/6e960330-2651-430a-95cb-acac86397508)

## run a pod
```bash
# kubectl run nginx --image=nginx:latest
pod/nginx created

# kubectl get pod
NAME        READY   STATUS    RESTARTS   AGE
nginx       1/1     Running   0          6s

#kubectl get pod -o wide (with detail)
NAME        READY   STATUS    RESTARTS   AGE     IP            NODE                          NOMINATED NODE   READINESS GATES
nginx       1/1     Running   0          7m14s   10.244.0.15   milad-cluster-control-plane   <none>           <none>
nginx-pod   1/1     Running   0          4d21h   10.244.0.11   milad-cluster-control-plane   <none>           <none>
```

همیشه وقتی پاد را بالا میاوریم توصیه میشه که بالاسرش یک ورکلود بالا آوریم . deployment , daemonset,replicaset 
برای اینکه وقتی پاد را تنها بالا میاوریم هیچ کنترل منیجیری ندارد که آن را نگاه کند برای همین ما از یک ورک لود استفاده میکنیم که یک کنترلری بالاسرش باشه تا پادمان در صورتی که مشکل خورد درستش کند .

```bash
kubectl detele pod nginx
```
پاد پاک میشود و کسی نیست که پاد را بالا آورد .

## Edit pod
```bash
 kubectl edit pod nginx
```

ماتی کانتینر ها داخل پاد معمولا رول های مختلف دارند مثل :
1- init container
در محیط‌های کانتینریزیشن (Containerization) مانند Kubernetes، یک Init Container (کانتینر شروع) یک کانتینر است که قبل از اجرای کانتینر اصلی (Main Container) اجرا می‌شود. Init Container معمولاً برای انجام کارهایی مانند پیکربندی یا آماده‌سازی محیط قبل از اجرای کانتینر اصلی به‌کار می‌رود.

به عبارت دیگر، Init Container یک مرحله اولیه است که قبل از شروع کار کانتینر اصلی اجرا می‌شود و مسئولیت‌هایی مانند تهیه داده‌های مورد نیاز، ایجاد یا بررسی دیتابیس، اجرای اسکریپت‌های پیکربندی، یا هر کار پیش‌تری که لازم است برای آماده‌سازی محیط اجرای کانتینر اصلی انجام شود، دارد.

   

## Static pod
پادهایی که kuebelete آنها را بالا میاورد .
سوال اینجا پیش میاید که control manager , etcd , api server را چه کسی بالا میاورد .
در  داخل /etc/kubernetes/manifest  هر manifesti را داخل این دایرکتوری بزاریم kubelete وقتی اجرا میشود آنها را اجرا میکند .
به این پاد هایی که داخل این دایرکتوری تویط kubelete اجرا میشوند را static pod گویند .
```bash
/# ls -lha /etc/kubernetes/manifests/
-rw------- 1 root root 2.4K Nov 12 13:56 etcd.yaml
-rw------- 1 root root 3.8K Nov 12 13:56 kube-apiserver.yaml
-rw------- 1 root root 3.4K Nov 12 13:56 kube-controller-manager.yaml
-rw------- 1 root root 1.5K Nov 12 13:56 kube-scheduler.yaml
```
