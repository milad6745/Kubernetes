حتماً! نصب **کلاستر Kubernetes با استفاده از Kubespray** یکی از روش‌های رایج برای پیاده‌سازی Kubernetes به‌صورت قابل تنظیم و production-ready است. در ادامه توضیح کامل مراحل نصب آورده شده:

---

### ✅ Kubespray چیست؟

Kubespray یک پروژه اپن‌سورس مبتنی بر **Ansible** است که به شما امکان می‌دهد تا کلاستر Kubernetes را روی چندین ماشین (physical یا VM) نصب و پیکربندی کنید. مزیت اصلی آن **قابلیت تنظیم بالا** و **سازگاری با توزیع‌های مختلف لینوکس** است.

---

### 🛠 پیش‌نیازها

قبل از شروع، مطمئن شوید موارد زیر فراهم است:

1. **ماشین‌ها (یا VMها)**:

   * حداقل 2 ماشین (1 مستر و 1 نود کارگر).
   * سیستم‌عامل: Ubuntu, CentOS, Debian یا هر توزیع پشتیبانی‌شده دیگر.
   * SSH دسترسی بین نودها.

2. **نصب پکیج‌ها روی سیستم کنترل‌کننده (که Ansible اجرا می‌شود):**

   ```bash
   sudo apt update && sudo apt install -y python3-pip git sshpass
   pip3 install ansible
   ```

---

### 📦 مراحل نصب Kubespray

#### 1. کلون کردن مخزن Kubespray:

```bash
git clone https://github.com/kubernetes-sigs/kubespray.git
cd kubespray
```

#### 2. نصب وابستگی‌های مورد نیاز:

```bash
sudo pip3 install -r requirements.txt
```

#### 3. کپی فایل inventory نمونه و ساخت inventory اختصاصی:

```bash
cp -rfp inventory/sample inventory/mycluster
```

#### 4. تعریف نودها در فایل `inventory/mycluster/hosts.yaml`

ساخت این فایل را می‌توانید با اسکریپت زیر انجام دهید (فرض کنید 3 نود دارید):

```bash
declare -a IPS=(192.168.1.10 192.168.1.11 192.168.1.12)

CONFIG_FILE=inventory/mycluster/hosts.yaml
python3 contrib/inventory_builder/inventory.py ${IPS[@]} > $CONFIG_FILE
```

سپس فایل را ویرایش کنید تا مشخص شود کدام نود master و کدام worker است.

#### 5. اجرای نصب با Ansible:

```bash
ansible-playbook -i inventory/mycluster/hosts.yaml --become --become-user=root cluster.yml
```

---

### ✅ پس از نصب:

1. فایل kubeconfig در `~/.kube/config` ایجاد می‌شود.
2. با استفاده از `kubectl` می‌توانید وضعیت کلاستر را بررسی کنید:

   ```bash
   kubectl get nodes
   ```

---

### 💡 نکات مهم:

* اگر فایروال دارید، پورت‌ها را باز کنید (مانند 6443, 2379-2380 و ...).
* می‌توانید با تغییر پارامترها در `inventory/mycluster/group_vars` کلاستر را شخصی‌سازی کنید (شبکه، پلاگین CNI، نسخه K8s و...).
* Kubespray قابلیت نصب add-onهایی مثل Helm, MetalLB, ingress controller و ... را دارد.

---

پرسش عالیه! مقایسه بین **Kubespray** و **kubeadm** خیلی مهمه برای انتخاب روش نصب مناسب Kubernetes. جواب کوتاه:

> ✅ **Kubespray راحت‌تر نیست، ولی خودکارتر و پیشرفته‌تره** – مناسب برای نصب حرفه‌ای، در مقیاس بزرگ یا در محیط production.

---

### 🔍 مقایسه Kubespray و kubeadm:

| ویژگی                   | **kubeadm**                            | **Kubespray**                         |
| ----------------------- | -------------------------------------- | ------------------------------------- |
| نحوه نصب                | دستی (دستور به دستور)                  | خودکار با Ansible                     |
| مناسب برای              | محیط آزمایش و یادگیری، نصب ساده        | محیط production، چند نود، سفارشی‌سازی |
| مدیریت نودها            | دستی (خودت باید نود اضافه/حذف کنی)     | خودکار از طریق Ansible                |
| پیکربندی شبکه           | باید خودت نصب کنی (Flannel, Calico...) | اتوماتیک پیکربندی می‌کنه              |
| نیاز به دانش لینوکس     | متوسط                                  | متوسط به بالا (مخصوصاً Ansible)       |
| قابلیت سفارشی‌سازی      | محدود                                  | زیاد (از طریق فایل‌های YAML)          |
| نیاز به نرم‌افزار اضافی | فقط kubeadm و kubectl و Docker         | Ansible، Python، Kubespray            |

---

### ✅ پس نتیجه‌گیری:

* اگر تازه‌کاری یا کلاستر کوچیک داری → **kubeadm راحت‌تره** چون با چند دستور ساده راه می‌افته.
* اگر می‌خوای **کلاستر قابل اطمینان، چند نودی، با کنترل کامل روی تنظیمات** داشته باشی → **Kubespray بهتره** چون همه‌چیزو خودکار و دقیق انجام می‌ده.

---





