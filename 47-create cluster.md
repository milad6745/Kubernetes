How To Setup Kubernetes Cluster Using Kubeadm

source[https://devopscube.com/setup-kubernetes-cluster-kubeadm/]



عالیه، بریم سر اصل مطلب: 👷‍♂️ **نصب Worker Node و اتصالش به کلاستر Kubernetes با kubeadm**.

---

## ✅ مرحله‌به‌مرحله اضافه کردن یک Worker Node

فرض کن که Master کلاستر رو ساختی و حالا می‌خوای یه Worker رو بهش وصل کنی.

---

### 🖥️ روی Worker Node این مراحل رو انجام بده:

#### 1. **غیرفعال کردن swap** (مثل master):
```bash
sudo swapoff -a
sudo sed -i '/ swap / s/^/#/' /etc/fstab
```

---

#### 2. **نصب ابزارهای مورد نیاز**:
```bash
sudo apt update
sudo apt install -y docker.io
sudo systemctl enable docker && sudo systemctl start docker
```

سپس:

```bash
sudo apt install -y apt-transport-https curl
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
```

بعد از اضافه کردن ریپو:

```bash
echo "deb http://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
sudo apt update
sudo apt install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
```

---

#### 3. **دریافت دستور `kubeadm join` از master**

روی Master Node این دستور رو بزن:

```bash
kubeadm token create --print-join-command
```

خروجی‌ش یه چیزی شبیه این خواهد بود:

```bash
kubeadm join 192.168.56.10:6443 --token abcdef.0123456789abcdef \
    --discovery-token-ca-cert-hash sha256:123456abcdef...
```

---

#### 4. **اجرای دستور join روی Worker Node**:

فقط کافیه دستور بالا رو **عیناً روی Worker Node اجرا کنی**:

```bash
kubeadm join 192.168.56.10:6443 --token abcdef.0123456789abcdef \
    --discovery-token-ca-cert-hash sha256:123456abcdef...
```

اگه همه چیز درست باشه، Worker نود به کلاستر وصل می‌شه 🎉

---

### 🔍 تست اتصال در master:

روی مستر این دستور رو بزن:

```bash
kubectl get nodes
```

باید اسم Worker Node رو ببینی که joined شده.

---

اگه دوست داشتی برات یه اسکریپت اتوماتیک نصب بنویسم برای Workerها یا کمک خواستی توی پیدا کردن join token، کافیه بگی.
