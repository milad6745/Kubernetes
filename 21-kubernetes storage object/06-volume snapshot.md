<div dir="rtl">


حتماً! در ادامه نسخه‌ای مرتب، راست‌چین و سازگار با Git (Markdown یا فایل متنی) از نوشته شما ارائه شده است که هم ساختار YAMLها حفظ شده، هم دیاگرام قابل خواندن است، و متن نیز به صورت صحیح راست‌چین شده:

---

## 📌 معرفی VolumeSnapshot در Kubernetes

در Kubernetes، مفهوم **Volume Snapshot** به نسخه‌ای از یک حجم داده (Volume) در یک لحظه خاص اشاره دارد. این قابلیت امکان پشتیبان‌گیری، بازگردانی و استفاده مجدد از داده‌ها را فراهم می‌کند.

### 📂 منابع مرتبط با Volume Snapshot:

- **`VolumeSnapshot` و `VolumeSnapshotClass`**:  
  برای تعریف Snapshot و تعیین تنظیمات مربوط به آن.
  
- **`VolumeSnapshotContent`**:  
  مشخص می‌کند Snapshot چگونه ذخیره می‌شود و امکان استفاده برای ایجاد PVC جدید را فراهم می‌کند.

---

## 💡 سناریو: ایجاد Snapshot از یک Volume موجود

فرض کنید یک برنامه در حال اجراست که از یک PVC برای ذخیره اطلاعات استفاده می‌کند. حالا قصد داریم از این PVC یک Snapshot بگیریم.

### ۱. تعریف `PersistentVolumeClaim` (PVC)

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
```

---

### ۲. استفاده از PVC در Pod

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
    - name: my-container
      image: my-app-image
      volumeMounts:
        - mountPath: "/data"
          name: my-volume
  volumes:
    - name: my-volume
      persistentVolumeClaim:
        claimName: my-pvc
```

---

### ۳. ایجاد VolumeSnapshot

```yaml
apiVersion: snapshot.storage.k8s.io/v1
kind: VolumeSnapshot
metadata:
  name: my-snapshot
spec:
  source:
    persistentVolumeClaimName: my-pvc
```

---

### ۴. بازیابی از VolumeSnapshot با استفاده از PVC جدید

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: restored-pvc
spec:
  dataSource:
    name: my-snapshot
    kind: VolumeSnapshot
    apiGroup: snapshot.storage.k8s.io
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
```

---

## 🧱 دیاگرام منابع VolumeSnapshot

```
                        +-------------------------+
                        |   VolumeSnapshotClass   |
                        |-------------------------|
                        | - Name                  |
                        | - Driver                |
                        | - DeletionPolicy        |
                        +------------+------------+
                                     |
                                     | Uses
                                     v
                        +-------------------------+
                        |     VolumeSnapshot       |
                        |-------------------------|
                        | - Name                  |
                        | - Source (PVC)          |
                        | - SnapshotClassName     |
                        +------------+------------+
                                     |
                                     | Creates
                                     v
                        +-----------------------------+
                        |   VolumeSnapshotContent      |
                        |-----------------------------|
                        | - Name                      |
                        | - SnapshotHandle            |
                        | - Source (PVC)              |
                        | - SnapshotData              |
                        +-----------------------------+
```

---
</div>
