

## **1. نصب `calicoctl`**  
ابتدا ابزار `calicoctl` را دانلود و نصب کن:  
```bash
curl -O -L https://github.com/projectcalico/calico/releases/latest/download/calicoctl-linux-amd64
chmod +x calicoctl-linux-amd64
sudo mv calicoctl-linux-amd64 /usr/local/bin/calicoctl
```

برای بررسی اینکه ابزار درست نصب شده، این دستور را اجرا کن:  
```bash
calicoctl version
```

---

## **2. تنظیم `calicoctl` برای اجرا در Kubernetes**
به‌صورت پیش‌فرض، `calicoctl` نمی‌داند که باید در حالت **Kubernetes Datastore** اجرا شود. بنابراین، باید متغیر محیطی را تنظیم کنیم:

```bash
export DATASTORE_TYPE=kubernetes
export KUBECONFIG=~/.kube/config
```
حالا می‌توانی دستورات `calicoctl` را اجرا کنی.

---

## **3. بررسی Poolهای آدرس IP**
برای بررسی محدوده‌های آدرس IP که Calico مدیریت می‌کند، از این دستور استفاده کن:
```bash
calicoctl get ippool
```
اگر خروجی مشابه زیر دیدی، یعنی Calico در حال کار است:
```
NAME                  CIDR             SELECTOR
default-ipv4-ippool   192.168.0.0/16   all()
```
اگر خروجی خالی بود، یعنی Calico به درستی تنظیم نشده است.

---

## **4. بررسی اطلاعات گره‌ها (Nodes)**
```bash
calicoctl get nodes
```
این دستور باید لیست گره‌های کلاستر را نمایش دهد.

---

## **5. تست ارتباط بین Podها با `calicoctl`**
اگر می‌خواهی ببینی آیا **پادها مجاز به ارتباط با یکدیگر هستند یا نه**، می‌توانی این تست را انجام دهی:

```bash
calicoctl connectivity check
```
اگر **همه چیز درست باشد**، خروجی مشابه این را می‌بینی:
```
✓ Node-to-node connectivity is working
✓ Pod-to-Pod connectivity is working
```
اگر مشکل وجود داشته باشد، پیام‌های خطا نشان داده می‌شوند.

---

## **6. بررسی قوانین `NetworkPolicy`**
اگر می‌خواهی ببینی که چه **Policy**هایی روی شبکه اعمال شده‌اند، می‌توانی این دستور را اجرا کنی:

```bash
calicoctl get policy
```
اگر **Policy** خاصی نداشته باشی، خروجی خالی خواهد بود. در غیر این صورت، قوانینی که Calico برای کنترل ترافیک بین **Pod**ها اعمال کرده، نمایش داده می‌شود.

---

### **جمع‌بندی**
✅ **`calicoctl get ippool`** → بررسی Poolهای آدرس IP  
✅ **`calicoctl get nodes`** → بررسی گره‌های شبکه  
✅ **`calicoctl connectivity check`** → تست ارتباط بین Nodeها و Podها  
✅ **`calicoctl get policy`** → نمایش قوانین **Network Policy**  

