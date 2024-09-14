# ingress

یک اسمارت روتر است که به ما اجازه میده ترافیک بیرون را به داخل داشته باشیم.
بهترین روش برای ماست 
شبیه reverse proxy کار میکند.

درخواست ها در ابتدا به ingress سپس به Service و در نهایت به پاد ها میرسد .

![image](https://github.com/milad6745/Kubernetes/assets/113288076/53cafa00-5a39-48b8-ac7b-f69c6271b9fd)

![image](https://github.com/milad6745/Kubernetes/assets/113288076/5b24c8e2-47e9-4e03-abbd-c96c7a9822d8)



یکی از مفاهیم مهم در Kubernetes است که به کاربران خارجی اجازه می دهد با استفاده از FQDN (نام دامنه کاملاً واجد شرایط) به برنامه های کانتینر شده دسترسی پیدا کنند. Ingress به طور پیش فرض در کلاستر Kubernetes فعال و نصب نشده است. ما باید با استفاده از کنترل کننده های ورودی third party مانند Nginx ، Traefik ، HAProxy و غیره این مفهوم را فعال کنیم. در این آموزش نشان خواهیم داد که چگونه می توانیم NGINX Ingress controller را در کلاستر Kubernetes فعال و استفاده کنیم.

<img width="321" alt="NGINX-Ingress-Controller-Kubernetes" src="https://github.com/user-attachments/assets/eb7a208d-561c-4c2d-ad05-2ceded46cf36">

همانطور که در تصویر بالا می‌بینید ، کاربران خارجی با استفاده از NGINX Ingress Controller از طریق FQDN به برنامه ها دسترسی پیدا می کنند و ingress controller داخلی درخواست را به سرویس هدایت می کند و سپس سرویس درخواست را برای backend یا Pod ها هدایت می کند.

 **فعال کردن NGINX Ingress Controller در Minikube**
Minikube

یک کلاستر Kubernetes با یک Node است که می توانیم به راحتی با اجرای دستورات زیر nginx ingress controller را در minikube فعال کنیم.

اگر با نحوه نصب minikube آشنایی ندارید، می‌توانید مطلب “نحوه نصب Kubernetes (k8s) با Minikube در CentOS 8 ” را دنبال کنید.

برای تأیید وضعیت ingress controller دستور زیر را اجرا کنید
```
minikube addons list
minikube addons enable ingress
```

kubectl get pods --all-namespaces | grep -i nginx-controller
![Screenshot-from-2020-12-15-21-58-28](https://github.com/user-attachments/assets/3203f1c8-a19c-4b6c-b38a-91b00898c50a)

**تنظیم NGINX Ingress Controller در کلاستر Kubernetes**


```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/master/deploy/static/provider/baremetal/deploy.yaml
```

برای بررسی وضعیت پادهای nginx-ingress controller دستور زیر را اجرا کنید

```
kubectl get pods -n ingress-nginx
```

برای تست یک مانیفست ایجاد میکنیم.
استقرار httpd و سرویس آن را با نوع NodePort روی درگاه 80 پیاده سازی کنید، فایل yaml زیر را که شامل بخش استقرار و سرویس است ایجاد کنید:


```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: httpd-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      run: httpd-deployment
  template:
    metadata:
      labels:
        run: httpd-deployment
    spec:
      containers:
      - image: httpd
        name: httpd-webserver

---
apiVersion: v1
kind: Service
metadata:
  name: httpd-service
spec:
  type: ClusterIP
  selector:
    run: httpd-deployment
  ports:
    - port: 8
```

استقرار nginx و سرویس آن را با نوع NodePort روی درگاه 80 پیاده سازی کنید، فایل yaml زیر را که شامل بخش استقرار و سرویس است ایجاد کنید:

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      run: nginx-deployment
  template:
    metadata:
      labels:
        run: nginx-deployment
    spec:
      containers:
      - image: nginx
        name: nginx-webserver

---
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  type: ClusterIP
  selector:
    run: nginx-deployment
  ports:
    - port: 80
```

**ساخت ingress , استقرار**

```
apiVersion: networking.k8s.io/v1
 kind: Ingress
 metadata:
   name: name-based-virtualhost-ingress
 spec:
   rules:
 host: httpd.example.com
 http:
   paths:
 pathType: Prefix
 path: "/"
 backend:
   service:
     name: httpd-service
     port:
       number: 80
 host: nginx.example.com
 http:
   paths:
 pathType: Prefix
 path: "/"
 backend:
   service:
     name: nginx-service
     port:
       number: 80
```



```
kubectl describe ingress name-based-virtualhost-ingress
kubectl describe ingress name-based-virtualhost-ingress
```


قبل از دسترسی به این url ها از خارج از کلاستر، لطفا مطمئن شوید که ورودی های زیر را در فایل hosts سیستم خود اضافه کردید.
```
192.168.1.190                httpd.example.com
192.168.1.190                nginx.example.com
```
![03](https://github.com/user-attachments/assets/dd48dcd5-2fba-4b31-91a9-b3dae90c3c27)
![04](https://github.com/user-attachments/assets/00d75480-e343-4823-9002-53ffc88c9f58)


