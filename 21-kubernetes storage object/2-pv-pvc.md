# pv
در Kubernetes، `PersistentVolume` (PV) یک منبع ذخیره‌سازی است که مستقل از Pod ایجاد می‌شود و از آن می‌توان در Pod‌ها استفاده کرد. PV به عنوان یک منبع دائمی برای ذخیره‌سازی داده‌ها استفاده می‌شود و برای حل مشکلات نگهداری دائمی داده‌ها در محیط‌های کانتینری کاربرد دارد.

در واقع، PV یک نوع منبع ذخیره‌سازی است که از تاریخ ایجاد خود (creation date) مستقل است و به طور جداگانه تعریف می‌شود. این امکان را فراهم می‌کند که PV‌ها مجزا از Pod‌ها و استفاده از PVC (PersistentVolumeClaim) قابل مدیریت باشند.

برخی ویژگی‌های مهم PV:

1. **نوع دسترسی (Access Modes):**
   - PV دارای نوع‌های دسترسی مختلف است که تعیین می‌کنند چه تعداد Pod می‌توانند به یک PV به صورت همزمان دسترسی داشته باشند. این نوع‌ها شامل `ReadWriteOnce` (یکبار خواندن و نوشتن توسط یک Pod)، `ReadOnlyMany` (تنها قابلیت خواندن توسط چندین Pod) و `ReadWriteMany` (قابلیت خواندن و نوشتن توسط چندین Pod) هستند.

2. **ظرفیت (Capacity):**
   - PV دارای یک ظرفیت مشخص (به عنوان مثال، تعداد گیگابایت) برای ذخیره‌سازی داده‌ها است.

3. **منابع (Resources):**
   - PV می‌تواند به عنوان یک دستگاه ذخیره‌سازی خاص (مانند یک دیسک) یا به عنوان یک فضای ذخیره‌سازی شبکه (مانند NFS) تعریف شود.

4. **حالت حجم (Volume Mode):**
   - تعیین می‌کند که حجم PV باید به عنوان یک دایرکتوری یا یک دستگاه (block device) دسترسی پیدا کند.

5. **دوره حیات (Lifecycle):**
   - PV دارای دوره حیات است که از ایجاد تا حذف تعیین می‌شود. PV ممکن است در حالت "Available" (در دسترس)، "Bound" (متصل به یک PVC) یا "Released" (آزاد شده ولی هنوز در دسترسی است) قرار گیرد.

یک نمونه ساده از یک تعریف PV در Kubernetes به صورت YAML:

حالا که یک PersistentVolume (PV) با نام `example-pv` ایجاد کرده‌اید، می‌توانید یک PersistentVolumeClaim (PVC) بسازید و از آن در یک Pod استفاده کنید. در اینجا یک مثال از یک PVC و Pod برای استفاده از PV شما آورده شده است:

### 1. ایجاد PersistentVolumeClaim (PVC):


```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: example-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
```

در این مثال، PVC با نام `example-pvc` با دسترسی به `ReadWriteOnce` و ظرفیت 1 گیگابایت ایجاد شده است.

### 2. ایجاد Pod:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: example-pod
spec:
  containers:
  - name: nginx-container
    image: nginx
    volumeMounts:
    - mountPath: "/usr/share/nginx/html"
      name: storage-volume
  volumes:
  - name: storage-volume
    persistentVolumeClaim:
      claimName: example-pvc
```

```yaml
kubectl describe pod
Volumes:
  storage-volume:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  example-pvc
    ReadOnly:   false
  kube-api-access-bzxnk:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
```

در این مثال، یک Pod با نام `example-pod` و یک کانتینر از تصویر `nginx` ایجاد شده است. این Pod از یک Volume به نام `storage-volume` با استفاده از PVC به نام `example-pvc` استفاده می‌کند. این PVC به PV شما (`example-pv`) متصل شده و حجم مشخص شده در PV را به Pod ارائه می‌دهد.

حالا می‌توانید این Pod را با استفاده از دستور `kubectl apply -f pod-manifest.yaml` به کلاستر Kubernetes اعمال کرده و عملکرد آن را بررسی کنید.


## Example


 برای اینکه بتوان از PVها استفاده کرد می‌بایست از persistentVolumeClaim استفاده کرد. به عبارت دیگر نمی‌توان در ساختار Pod به صورت مستقیم PV را فراخوانی و از آنها استفاده کرد. همانطور که توضیحات ارائه شده PV‌های به صورت cluster wide است و توسط ادمین‌ها در سطح کلاستر تعریف می‌شوند، و برای اینکه بتوان از PV‌ها برای والیوم‌ها در ساختار پاد استفاده کرد، می‌بایست از مفهومی‌به نام persistentVolumeClaim یا PVC بهره مند شد. به بیانی دیگر کاربر در سطح کلاستر کوبرنتیز PVC می‌سازد و با کمک PVC می‌تواند PV که توسط ادمین ساخته شده است را فراخانی کند. همچنین لازم به ذکر است که PVC به صورتname space base است، یعنی هر کاربری می‌تواند در name space جداگانه خود اقدام به ساخت PVC مورد نیاز خود کند.
![Uploading image.png…]()



 

برای ایجاد یک `PersistentVolume` (PV) و متصل کردن یک `PersistentVolumeClaim` (PVC) و یک `Pod` به آن، ابتدا یک مانیفست برای هرکدام از اینها ایجاد کنید. در زیر یک مثال ارائه شده است:

### 1. ایجاد PersistentVolume (pv.yaml):

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: example-pv
spec:
  capacity:
    storage: 1Gi
  volumeMode: Filesystem
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: "/mnt/data"
```

### 2. ایجاد PersistentVolumeClaim (pvc.yaml):

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: example-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
```

### 3. ایجاد Pod (pod.yaml):

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: example-pod
spec:
  containers:
  - name: nginx-container
    image: nginx
    volumeMounts:
    - mountPath: "/usr/share/nginx/html"
      name: storage-volume
  volumes:
  - name: storage-volume
    persistentVolumeClaim:
      claimName: example-pvc
```

### 4. اعمال مانیفست‌ها:

```bash
kubectl apply -f pv.yaml
kubectl apply -f pvc.yaml
kubectl apply -f pod.yaml
```

```
kubectl get pv
NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM                  STORAGECLASS   REASON   AGE
example-pv                                 1Gi        RWO            Retain           Available                                                  21h

kubectl get pvc
NAME           STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
example-pvc    Bound    pvc-198a3865-3cd5-4e70-9242-ab3d1c138e12   1Gi        RWO            standard       20h
```
در این مثال:

- `PersistentVolume` با نام `example-pv` ایجاد می‌شود که از نوع `hostPath` و با ظرفیت 1 گیگابایت است. مسیر فایل‌ها در اینجا به `/mnt/data` تنظیم شده است.

- `PersistentVolumeClaim` با نام `example-pvc` ایجاد می‌شود که از `PersistentVolume` مرتبط (`example-pv`) استفاده می‌کند.

- `Pod` با نام `example-pod` ایجاد می‌شود که از `PersistentVolumeClaim` (`example-pvc`) به عنوان یک VolumeMount استفاده می‌کند و این Volume را در مسیر `/usr/share/nginx/html` درون کانتینر Mount می‌کند.

با اجرای این مراحل، یک `Pod` با یک `PersistentVolume` متصل خواهد شد. مطمئن شوید که مسیر `/mnt/data` بر روی کلاستر Kubernetes شما وجود دارد و مجوزهای لازم برای آن تنظیم شده است.
