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
![image](https://github.com/milad6745/Kubernetes/assets/113288076/d50d5b14-173d-4197-a3b0-902c4ad52c56)

## pod phase
   

**Pending (در انتظار):** 

Pod در حال ایجاد است و منابع مورد نیاز برای اجرای کانتینرها در حال جذب شدن هستند.

**Running (در حال اجرا):**

تمامی کانتینرهای Pod اجرا شده‌اند و در حال حاضر در حال اجرای کد هستند.

**Succeeded (موفق):**

تمامی کانتینرهای Pod با موفقیت اجرا شده‌اند و اجرای آنها به پایان رسیده است.

**Failed (ناموفق):** 

حداقل یکی از کانتینرهای Pod با خطا مواجه شده است و اجرای آنها به پایان رسیده است.

**Unknown (ناشناخته):** 

وضعیت Pod به‌دلیل مشکلات ارتباطی یا دلایل دیگر قابل تشخیص نیست.


## Example init container

create a pod with two container with init container
```yml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - name: mynginxcontainer
    image: nginx:latest
    ports:
    - containerPort: 80
  initContainers:
  - name: myinitcontainer
    image: busybox:latest
    command: ['sh', '-c', 'echo "Init Container is running"']
```

```bash
# kubectl describe pod mypod
Init Containers:
  myinitcontainer:
    Container ID:  containerd://5983265b635ea507298d67705ec64bd42640746bc6542dc2ac70996e34b1ab77
    Image:         busybox:latest
    Image ID:      docker.io/library/busybox@sha256:3fbc632167424a6d997e74f52b878d7cc478225cffac6bc977eedfe51c7f4e79
    Port:          <none>
    Host Port:     <none>
    Command:
      sh
      -c
      echo "Init Container is running"
    State:          Terminated
      Reason:       Completed
      Exit Code:    0
      Started:      Sun, 19 Nov 2023 20:15:13 +0300
      Finished:     Sun, 19 Nov 2023 20:15:13 +0300
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-g4m5r (ro)
Containers:
  mynginxcontainer:
    Container ID:   containerd://2d37cbb64bb65fb0b9c70bee44ed5268e2e0acd9032d4960022922314ac03f93
    Image:          nginx:latest
    Image ID:       docker.io/library/nginx@sha256:86e53c4c16a6a276b204b0fd3a8143d86547c967dc8258b3d47c3a21bb68d3c6
    Port:           80/TCP
    Host Port:      0/TCP
    State:          Running
      Started:      Sun, 19 Nov 2023 20:15:16 +0300
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-g4m5r (ro)
```
# Example multi container on a pod
```bash
apiVersion: v1
kind: Pod
metadata:
  name: mc1
spec:
  volumes:
  - name: html
    emptyDir: {}
  containers:
  - name: 1st
    image: nginx
    volumeMounts:
    - name: html
      mountPath: /usr/share/nginx/html
  - name: 2nd
    image: debian
    volumeMounts:
    - name: html
      mountPath: /html
    command: ["/bin/sh", "-c"]
    args:
      - while true; do
          date >> /html/index.html;
          sleep 1;
        done
```
