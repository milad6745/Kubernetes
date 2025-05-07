

### 🧩 مرحله 1: نصب `bash-completion`

**اگر از Ubuntu یا Debian هستی:**

```bash
sudo apt-get update
sudo apt-get install bash-completion
```

**اگر از CentOS/RHEL هستی:**

```bash
sudo yum install bash-completion
```

---

### 🧩 مرحله 2: فعال کردن `bash-completion`

بعد از نصب، فایل bashrc رو باز کن و اینو اضافه کن:

```bash
# فعال‌سازی bash-completion (اگر هنوز فعال نیست)
if [ -f /etc/bash_completion ]; then
    . /etc/bash_completion
fi

# فعال‌سازی auto-completion برای kubectl
source <(kubectl completion bash)
```

بعد با دستور زیر bash رو reload کن یا دوباره لاگین کن:

```bash
source ~/.bashrc
```

---

### 🔄 حالا تست کن:

```bash
kubectl descr[TAB]
```
