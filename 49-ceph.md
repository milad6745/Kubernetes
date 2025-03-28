# create cluster with minimum 2 worker

فهمیدم! اگر قصد داری که از **Rook-Ceph** برای نصب Ceph در کلاستر Kubernetes خود استفاده کنی، این به‌خصوص در استفاده از **`kind`** مناسب است. **Rook** یک **Operator** است که به‌طور خاص برای مدیریت کلاسترهای ذخیره‌سازی در Kubernetes طراحی شده و به راحتی می‌تواند Ceph را در کلاستر نصب، پیکربندی و مدیریت کند.

### مراحل نصب **Rook-Ceph** بر روی کلاستر **kind**:

1. **ساخت کلاستر Kubernetes با استفاده از `kind`**:
   اگر هنوز کلاستری با استفاده از `kind` نساختی، می‌تونی از کانفیگ زیر برای داشتن یک کلاستر با 1 نود `control-plane` و 2 نود `worker` استفاده کنی (که برای تست و توسعه مناسب است):

   ```yaml
   kind: Cluster
   apiVersion: kind.x-k8s.io/v1alpha4
   nodes:
     - role: control-plane
     - role: worker
     - role: worker
   ```

   سپس کلاستر رو بساز:

   ```bash
   kind create cluster --config kind-config.yaml
   ```

2. **نصب Rook-Ceph**:
   برای نصب **Rook-Ceph** در کلاستر Kubernetes، باید ابتدا **Rook Operator** و منابع مورد نیاز آن را نصب کنی. این کار با اجرای دستوراتی که در اینجا آورده شده انجام می‌شود.

   ابتدا **namespace** مربوط به `rook-ceph` رو بساز:

   ```bash
   kubectl create namespace rook-ceph
   ```

3. **نصب Rook-Ceph Operator**:
   حالا باید **Rook Operator** رو نصب کنی. برای نصب از دستور زیر استفاده کن:

   ```bash
   kubectl apply -f https://github.com/rook/rook/releases/download/v1.9.9/rook-ceph-operator.yaml
   ```

   این دستور Rook Operator را نصب می‌کند که به‌طور خودکار Ceph را روی کلاستر Kubernetes مدیریت می‌کند.

4. **ایجاد Ceph Cluster با استفاده از Rook**:
   حالا که Rook-Ceph نصب شد، باید یک فایل YAML برای ایجاد کلاستر Ceph تعریف کنی. در این فایل مشخص می‌کنی که Ceph در کلاستر Kubernetes چطور نصب و پیکربندی بشه.

   برای این کار، یک فایل YAML به نام `ceph-cluster.yaml` بساز که به شکل زیر است:

   ```yaml
   apiVersion: ceph.rook.io/v1
   kind: CephCluster
   metadata:
     name: ceph
     namespace: rook-ceph
   spec:
     cephVersion:
       image: ceph/ceph:v15
     dataDirHostPath: /var/lib/rook
     mon:
       count: 3
       allowMultiplePerNode: false
     storage:
       useAllNodes: true
       useAllDevices: true
     network:
       hostNetwork: false
   ```

   این فایل یک کلاستر Ceph با ۳ مانیتر (`mon`) ایجاد می‌کند و به تمامی نودها و دستگاه‌ها برای ذخیره‌سازی داده‌ها دسترسی می‌دهد.

5. **اعمال کانفیگ و ایجاد Ceph Cluster**:
   حالا فایل YAML برای ایجاد Ceph Cluster رو اعمال کن:

   ```bash
   kubectl apply -f ceph-cluster.yaml
   ```

   این کار باعث می‌شود که کلاستر Ceph در داخل کلاستر Kubernetes شما راه‌اندازی بشه.

6. **بررسی وضعیت پادهای Ceph**:
   بعد از اعمال کانفیگ، می‌توانی وضعیت پادهای مربوط به Ceph را بررسی کنی:

   ```bash
   kubectl -n rook-ceph get pod
   ```

   با این دستور، می‌توانی وضعیت نصب و اجرای پادهای مربوط به Ceph را مشاهده کنی. مطمئن شو که تمام پادها در حال اجرا هستند.

7. **استفاده از Ceph برای ذخیره‌سازی (Optional)**:
   حالا که Ceph راه‌اندازی شد، می‌توانی از آن به‌عنوان یک ذخیره‌ساز برای Kubernetes استفاده کنی. برای این کار باید یک **Persistent Volume (PV)** و **Persistent Volume Claim (PVC)** از Ceph ایجاد کنی.

   برای مثال، یک PVC می‌تواند به شکل زیر باشد:

   ```yaml
   apiVersion: v1
   kind: PersistentVolumeClaim
   metadata:
     name: ceph-pvc
   spec:
     accessModes:
       - ReadWriteOnce
     resources:
       requests:
         storage: 5Gi
     storageClassName: rook-ceph-block
   ```

   این فایل PVC را از Ceph ایجاد می‌کند که می‌توانی از آن در پادهای خود استفاده کنی.

### خلاصه مراحل نصب Rook-Ceph:

1. ساخت کلاستر Kubernetes با استفاده از `kind`.
2. نصب Rook-Ceph Operator.
3. ایجاد Ceph Cluster با استفاده از فایل YAML.
4. بررسی وضعیت پادها و کلاستر Ceph.
5. (اختیاری) استفاده از Ceph برای ذخیره‌سازی در Kubernetes.

### اگر سوالی داری:
اگر در هر مرحله مشکلی داشتی یا سوالی پیش آمد، حتماً بگو تا بیشتر راهنمایی کنم! 😊
