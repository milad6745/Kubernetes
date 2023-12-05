# Access Mode

در Kubernetes، Access Mode یا حالت دسترسی نحوه ارتباط بین یک `PersistentVolume` (PV) و `PersistentVolumeClaim` (PVC) را مشخص می‌کند. این Access Modes به تعداد کلاینت‌های مختلفی که می‌توانند به یک PV متصل شوند، اشاره دارند. سه نوع Access Mode اصلی وجود دارد:

**ReadWriteOnce (RWO):**

فقط یک کلاینت (یک Pod) به صورت همزمان می‌تواند به PV دسترسی داشته باشد.
این Access Mode برای سناریوهایی که یک Pod به صورت انحصاری به یک دیسک نیاز دارد، مانند فایل‌سیستم‌هاست.

**ReadOnlyMany (ROX):**

چندین کلاینت (چندین Pod) به صورت همزمان می‌توانند از PV خواندن کنند، اما نمی‌توانند در آن نوشته کنند.
این Access Mode مناسب برای سناریوهایی است که چندین Pod نیاز به دسترسی به یک دیسک دائمی (مانند یک مجموعه اطلاعات تنها خواندنی) دارند.

**ReadWriteMany (RWX):**


چندین کلاینت (چندین Pod) به صورت همزمان می‌توانند به PV دسترسی داشته باشند و هم خواندن و هم نوشتن داشته باشند.
این Access Mode معمولاً برای سناریوهایی که چندین Pod به یک دیسک به صورت همزمان نیاز دارند (مثل شبکه‌های فایل) مناسب است.


### مثال:

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

در این مثال، `accessModes` به `ReadWriteOnce` تنظیم شده است، که به معنای این است که تنها یک Pod به صورت همزمان می‌تواند به این `PersistentVolume` دسترسی داشته باشد. این Access Mode برای استفاده در سناریوهایی مانند استفاده انحصاری یک Pod از یک فایل‌سیستم مناسب است.

# Example


می‌توانید یک `PersistentVolume` (PV) با Access Mode "ReadOnlyMany" (ROX) ایجاد کنید و سپس یک پاد (Pod) را به آن متصل کنید. در اینجا یک مثال از چگونگی ایجاد یک PV ROX و یک پاد که از آن استفاده می‌کند آورده شده است.

### Manifest برای PersistentVolume (pv-rox.yaml):

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-example-rox
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadOnlyMany
  nfs:
    path: /path/to/nfs/share
    server: nfs-server-address
```

### Manifest برای Pod (pod-with-pv-rox.yaml):

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-pv-rox
spec:
  containers:
  - name: my-container
    image: nginx
    volumeMounts:
    - name: rox-volume
      mountPath: /mnt/data
  volumes:
  - name: rox-volume
    persistentVolumeClaim:
      claimName: my-rox-claim
```

### Manifest برای PersistentVolumeClaim (pvc-rox.yaml):

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-rox-claim
spec:
  accessModes:
    - ReadOnlyMany
  resources:
    requests:
      storage: 5Gi
```

در این مثال:

- `pv-rox.yaml`:
-
ایجاد یک `PersistentVolume` با Access Mode "ReadOnlyMany" که به یک مخزن NFS متصل است.
- `pvc-rox.yaml`:
- 
-
ایجاد یک `PersistentVolumeClaim` که به PV با Access Mode "ReadOnlyMany" متصل می‌شود.
- `pod-with-pv-rox.yaml`:
- 
-
ایجاد یک پاد (Pod) که از PVC مربوطه به عنوان یک volume استفاده می‌کند و این volume به PV با Access Mode "ReadOnlyMany" متصل است.

لطفاً مقادیر مسیر (path) و آدرس سرور NFS (server) را با مقادیر معتبر خود جایگزین کنید.

