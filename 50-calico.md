ا

برای غیرفعال کردن CNI پیش‌فرض و استفاده از `Calico`، شما باید کلاستر Kind را به صورت معمولی با پیکربندی پیش‌فرض ایجاد کرده و سپس CNI پیش‌فرض را حذف و `Calico` را نصب کنید.

### مراحل درست برای استفاده از `Calico` در Kind

1. **ایجاد کلاستر Kind با پیکربندی پیش‌فرض**:
   ابتدا یک کلاستر Kind با پیکربندی پیش‌فرض بسازید. فایل پیکربندی شما باید به صورت زیر باشد (بدون نیاز به تغییرات مربوط به `network`):

   فایل `config.yml` شما می‌تواند به این صورت باشد:
   ```yaml
   kind: Cluster
   apiVersion: kind.x-k8s.io/v1alpha4
   nodes:
     - role: control-plane
     - role: worker
   ```

   سپس کلاستر را ایجاد کنید:
   ```bash
   kind create cluster --config config.yml
   ```

2. **حذف CNI پیش‌فرض (kindnet)**:
   پس از ایجاد کلاستر، شما باید CNI پیش‌فرض (که `kindnet` است) را حذف کنید. برای این کار می‌توانید پاد `kindnet` را حذف کنید:
   ```bash
   kubectl delete pod -n kube-system -l k8s-app=kindnet
   ```

3. **نصب `Calico`**:
   حالا که CNI پیش‌فرض حذف شده، می‌توانید `Calico` را نصب کنید:
   ```bash
   kubectl apply -f https://docs.projectcalico.org/manifests/calico.yaml
   ```

4. **بررسی وضعیت**:
   پس از نصب `Calico`, وضعیت پادهای مربوطه را بررسی کنید:
   ```bash
   kubectl get pods -n kube-system
   ```

   شما باید پادهای مربوط به `calico-node` را مشاهده کنید.

5. **تست شبکه**:
   برای تست شبکه، می‌توانید یک پاد `nginx` ایجاد کرده و اتصال آن به اینترنت را بررسی کنید:
   ```bash
   kubectl run nginx --image=nginx --restart=Never
   kubectl exec -it nginx -- ping 8.8.8.8
   ```

با این روش شما CNI پیش‌فرض Kind را غیرفعال کرده و از `Calico` به عنوان CNI استفاده خواهید کرد. اگر سوالی داشتید یا مشکلی پیش آمد، خوشحال می‌شوم کمک کنم!
