# reclaim policy

Reclaim Policy 

(سیاست بازیابی) یک ویژگی در تعریف `PersistentVolume` (PV) در Kubernetes است که تعیین می‌کند که پس از حذف یک `PersistentVolumeClaim` (PVC)، فضای ذخیره‌سازی متصل به PV چگونه مدیریت شود. سیاست بازیابی با استفاده از مقدار `reclaimPolicy` تعیین می‌شود و می‌تواند یکی از سه مقدار زیر را داشته باشد:

**Retain:**

در این حالت، PV تلاش می‌کند تا فضای ذخیره‌سازی را در اختیار نگه دارد پس از حذف PVC.
هیچ عملیات پاک‌سازی انجام نمی‌شود و PV همچنان به عنوان یک `PersistentVolume` در دسترس خواهد بود. این می‌تواند برای مواردی مناسب باشد که نیاز به نگه‌داری داده‌ها پس از حذف یک PVC دارید.

**Delete:**

در این حالت، هنگامی که PVC حذف می‌شود، تمام فضای ذخیره‌سازی متصل به PV نیز حذف می‌شود.
این مقدار مناسب برای مواردی است که بعد از حذف PVC، داده‌ها نیاز به حفظ ندارند و می‌توانید مقدار پیش‌فرض باشد.

**Recycle:**

توجه: Deprecated (پیشنهاد می‌شود از آن استفاده نشود)
در حالت Recycle، سیستم می‌کوشد تا فضای ذخیره‌سازی PV را بازیابی و پاک‌سازی کند. این سیاست در ورژن‌های جدید Kubernetes به دلیل نقاط ضعف و ایراداتی که داشته است، deprecated شده است.

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
  reclaimPolicy: Retain
  hostPath:
    path: "/mnt/data"
```
وقتی ما یه PV میسازیم باید reclaim , Access Mode  اش را مشخص کنیم .

در این مثال، `reclaimPolicy` به `Retain` تنظیم شده است، که به معنای این است که PV تلاش می‌کند تا پس از حذف PVC، فضای ذخیره‌سازی را در اختیار نگه دارد.

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
