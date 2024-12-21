برای استفاده از **Volume Snapshot** گرفته‌شده و بازگرداندن داده‌ها، می‌توانید از یک **PersistentVolumeClaim (PVC)** جدید استفاده کنید که داده‌های خود را از Snapshot بازیابی کند. این فرآیند شامل مراحل زیر است:

---

### مراحل بازگردانی داده‌ها از Volume Snapshot

#### 1. بررسی Volume Snapshot موجود  
ابتدا مطمئن شوید که یک Volume Snapshot موجود دارید و نام آن را بدانید. می‌توانید با دستور زیر لیست Snapshotها را بررسی کنید:

```bash
kubectl get volumesnapshot
```

خروجی مشابه زیر خواهد بود:
```plaintext
NAME                  READYTOUSE   SOURCEPVC   CREATEDTIME
my-volume-snapshot    true         my-pvc      2024-12-20T12:00:00Z
```

در اینجا، `my-volume-snapshot` نام Snapshot است.

---

#### 2. ایجاد یک PVC جدید از Volume Snapshot  

برای بازگردانی داده‌ها، یک PVC جدید تعریف کنید و مشخص کنید که داده‌ها باید از Snapshot گرفته شوند.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-pvc-restored
spec:
  storageClassName: csi-storage-class
  dataSource:
    name: my-volume-snapshot
    kind: VolumeSnapshot
    apiGroup: snapshot.storage.k8s.io
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 10Gi
```

- **storageClassName:** نام StorageClass مرتبط با درایور CSI شما.
- **dataSource.name:** نام Volume Snapshot که می‌خواهید از آن استفاده کنید.
- **dataSource.kind:** همیشه باید `VolumeSnapshot` باشد.
- **resources.requests.storage:** اندازه Volume که باید حداقل برابر یا بیشتر از اندازه Volume اصلی باشد.

---

#### 3. اعمال فایل YAML  
فایل تعریف‌شده را با دستور زیر اعمال کنید:

```bash
kubectl apply -f restored-pvc.yaml
```

پس از اعمال، با دستور زیر بررسی کنید که PVC ساخته شده و آماده استفاده است:

```bash
kubectl get pvc my-pvc-restored
```

---

#### 4. استفاده از PVC بازگردانی‌شده در Pod  

می‌توانید این PVC را در Pod یا Deployment خود استفاده کنید. به‌عنوان مثال:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: test-pod
spec:
  containers:
    - name: app
      image: nginx
      volumeMounts:
        - mountPath: /data
          name: restored-volume
  volumes:
    - name: restored-volume
      persistentVolumeClaim:
        claimName: my-pvc-restored
```

---

### نکات کلیدی:
1. **سازگاری با Snapshot:** PVC بازگردانی‌شده باید از همان StorageClass و درایور CSI استفاده کند که برای Snapshot استفاده شده است.
2. **اندازه Volume:** اطمینان حاصل کنید که حجم PVC بازگردانی‌شده به اندازه یا بیشتر از Volume اصلی باشد.
3. **Snapshot آماده استفاده:** قبل از بازیابی، بررسی کنید که فیلد `READYTOUSE` در Volume Snapshot برابر `true` باشد.

اگر این مراحل را طی کنید، داده‌ها به‌درستی از Snapshot بازگردانی خواهند شد. اگر به مشکلی برخوردید، توضیح دهید تا کمک کنم! 🌟
