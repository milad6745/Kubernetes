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





برای تست عملکرد **Calico** در کلاستر **Kind** می‌تونی از روش‌های زیر استفاده کنی:

---

## **1. بررسی وضعیت پادهای Calico**
قبل از هر چیز، مطمئن شو که تمام پادهای مربوط به **Calico** در وضعیت `Running` هستند:

```bash
kubectl get pods -n kube-system | grep calico
```
**اگر همه پادها در وضعیت Running باشند، یعنی سرویس Calico اجرا شده است.**

---

## **2. بررسی وضعیت کانال‌های ارتباطی بین نودها**
با استفاده از **Calicoctl** یا `kubectl get nodes` وضعیت نودها را بررسی کن:

```bash
kubectl get nodes -o wide
```
خروجی باید شامل آدرس‌های IP داخلی نودها باشد. اگر اینجا **آدرس IP نودها نمایش داده نشود یا NotReady باشد، مشکل شبکه داری.**

---

## **3. تست ارتباط بین پادها**
می‌تونی دو پاد ساده ایجاد کنی و بررسی کنی که آیا می‌توانند همدیگر را `ping` کنند یا نه.

### **(الف) ایجاد دو پاد ساده**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: busybox1
spec:
  containers:
    - name: busybox
      image: busybox
      command: ["sleep", "3600"]
---
apiVersion: v1
kind: Pod
metadata:
  name: busybox2
spec:
  containers:
    - name: busybox
      image: busybox
      command: ["sleep", "3600"]
```
فایل را ذخیره کن (`pods.yml`) و اجرا کن:
```bash
kubectl apply -f pods.yml
```

### **(ب) تست `ping` بین دو پاد**
بعد از اجرای پادها، وارد یکی از آن‌ها شو و `ping` بگیر:

```bash
kubectl exec -it busybox1 -- sh
```
داخل پاد دستور زیر را بزن:
```sh
ping busybox2
```
اگر پاسخ `ping` دریافت شد، یعنی شبکه کار می‌کند. اگر خطای `Request timeout` گرفتی، یعنی مشکل شبکه داری.

---

## **4. بررسی مسیریابی (`calicoctl node status`)**
اگر `calicoctl` را نصب کردی، می‌تونی ببینی که وضعیت ارتباطات بین نودها چطور است:

```bash
calicoctl node status
```
باید خروجی‌ای شبیه این ببینی:

```
Calico process is running.
IPv4 BGP status
+---------------+--------------+-------+---------+
| PEER ADDRESS  | PEER TYPE    | STATE | SINCE   |
+---------------+--------------+-------+---------+
| 192.168.1.10  | global       | up    | 2m10s   |
| 192.168.1.11  | global       | up    | 2m10s   |
+---------------+--------------+-------+---------+
```
**اگر خروجی `up` نباشد، یعنی ارتباط BGP برقرار نیست و Calico کار نمی‌کند.**

---

## **5. بررسی قوانین `NetworkPolicy`**
اگر یک **NetworkPolicy** محدودکننده فعال باشه، ممکنه ارتباطات بین پادها بلاک شده باشه. لیست **NetworkPolicy**ها رو چک کن:

```bash
kubectl get networkpolicy -A
```
**اگر پالیسی‌های محدودکننده فعال بود، ممکنه نیاز باشه که تغییرشون بدی.**

---

## **6. بررسی `iptables` در نودها**
اگر هنوز مشکل داشتی، می‌تونی قوانین `iptables` رو در نودهای کلاستر بررسی کنی:

```bash
kubectl get nodes -o wide
```
سپس وارد یکی از نودها شو:
```bash
docker exec -it kind-control-plane sh
```
و `iptables` رو چک کن:
