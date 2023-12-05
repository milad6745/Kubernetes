# storage class

 در Kubernetes، `StorageClass` یک منبع تخصیص داده به عنوان PersistentVolume (PV) را تعریف می‌کند. `StorageClass` به کاربران اجازه می‌دهد تا برای استفاده از ذخیره‌سازی دائمی بدون نیاز به تنظیمات مستقیم بر روی `PersistentVolume` استفاده کنند.

به واسطه Storage class والیوم PV خودش ایجاد میشه و نیازی به کانفیگ ادمین نیست .

## Diff Between storage class and PV

### PV 
به صورت دستی توسط کاربر ایجاد می‌شود و یک PV خاص به یک PVC خاص متصل می‌شود.

### StorageClass
    
این امکان را فراهم می‌کند تا به صورت دینامیک بر اساس نیازهای کاربران PV ایجاد شود. کاربران با ایجاد یک PVC و استفاده از یک StorageClass، به سرعت و بدون نیاز به دخالت دستی، به فضایذخیره‌سازی دسترسی پیدا کنند.

به طور کلی، استفاده از StorageClass به منظور ایجاد یک سیاست دینامیک و انعطاف‌پذیر در مدیریت فضای ذخیره‌سازی توصیه می‌شود

---

ویژگی‌های مهم `StorageClass` عبارتند از:

**Provisioner:**

نحوه تأمین فضای ذخیره‌سازی. برای مثال، یک provisioner می‌تواند فضای ذخیره‌سازی را در یک Cloud Provider فراهم کند (برای مثال، در Azure یا AWS) یا از نوع محلی (بر اساس فایل‌های محلی) باشد.

   **Parameters:**
  
تنظیمات اختیاری که به provisioner ارسال می‌شوند.

**Reclaim Policy:**
  
نحوه مدیریت `PersistentVolume` ها بعد از حذف `PersistentVolumeClaim`. می‌تواند به سه حالت "Retain" (نگهداری داده‌ها)، "Delete" (حذف داده‌ها) یا "Recycle" (پاکسازی داده‌ها) تنظیم شود.
    

### مثال ایجاد یک StorageClass:

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: standard
provisioner: kubernetes.io/aws-ebs
parameters:
  type: gp2
reclaimPolicy: Retain
```

در این مثال:

- `provisioner`: نشان‌دهنده provisioner مورد استفاده (در اینجا `kubernetes.io/aws-ebs` برای AWS EBS).
  
- `parameters`: تنظیمات اختیاری که به provisioner ارسال می‌شوند (در اینجا `type` با مقدار `gp2`).

- `reclaimPolicy`: نحوه مدیریت `PersistentVolume` بعد از حذف `PersistentVolumeClaim` (در اینجا `Retain`).

می‌توانید از `StorageClass` در `PersistentVolumeClaim` استفاده کنید تا به صورت خودکار `PersistentVolume` برای شما ایجاد شود. به عبارت دیگر، `StorageClass` به کاربران این امکان را می‌دهد که بدون نگرانی از جزئیات فیزیکی ذخیره‌سازی، از منابع تخصیص داده شده به آنها استفاده کنند.


## Example

برای ایجاد یک `StorageClass` و یک `PersistentVolumeClaim` (PVC) که به آن متصل شود، می‌توانید از مثال زیر استفاده کنید:

### 1. ایجاد StorageClass (storage-class.yaml):

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: fast
provisioner: kubernetes.io/aws-ebs
parameters:
  type: gp2
reclaimPolicy: Retain
```

### 2. ایجاد PersistentVolumeClaim (pvc.yaml):

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-pvc
spec:
  accessModes:
    - ReadWriteOnce
  storageClassName: fast
  resources:
    requests:
      storage: 1Gi
```

### 3. ایجاد Pod که به PersistentVolumeClaim متصل شود (pod.yaml):

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
  - name: my-container
    image: nginx
    volumeMounts:
    - mountPath: "/usr/share/nginx/html"
      name: my-storage
  volumes:
  - name: my-storage
    persistentVolumeClaim:
      claimName: my-pvc
```

### 4. اعمال مانیفست‌ها:

```bash
kubectl apply -f storage-class.yaml
kubectl apply -f pvc.yaml
kubectl apply -f pod.yaml
```

در این مثال:

یک `StorageClass` به نام "fast" ایجاد شده است که از provisioner مربوط به AWS EBS (`kubernetes.io/aws-ebs`) استفاده می‌کند. ظرفیت درخواستی برای هر `PersistentVolumeClaim` تنظیم شده است.

یک `PersistentVolumeClaim` با نام "my-pvc" ایجاد شده است که به `StorageClass` "fast" متصل شده و 1 گیگابایت فضای ذخیره‌سازی درخواست می‌کند.

یک `Pod` با نام "my-pod" ایجاد شده است که به `PersistentVolumeClaim` "my-pvc" متصل شده و از فضای ذخیره‌سازی درون کانتینر استفاده می‌کند.

با اجرای این مراحل، یک `Pod` با یک `PersistentVolumeClaim` که از `StorageClass` مشخصی استفاده می‌کند ایجاد می‌شود.

```
kubectl get pv
example-pv                                 1Gi        RWO            Retain           Available                                                  24h
pvc-198a3865-3cd5-4e70-9242-ab3d1c138e12   1Gi        RWO            Delete           Bound       default/example-pvc    standard                22h
┌──(root💀kali)-[/cache]
└─# kubectl get pvc
NAME           STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
example-pvc    Bound    pvc-198a3865-3cd5-4e70-9242-ab3d1c138e12   1Gi        RWO            standard       22h
```

