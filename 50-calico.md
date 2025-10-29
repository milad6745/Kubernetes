حتماً! 🌟 من بر اساس اطلاعاتی که استخراج کردی و نکاتی که گفتیم، یک **خلاصه جزوه‌ای و مرتب درباره Calico در Kubernetes** برات آماده کردم که می‌تونی ذخیره و همیشه استفاده کنی:

---

# **جزوه Calico در Kubernetes**

## ۱. معرفی

* **Calico** یک **CNI Plugin** برای Kubernetes است که:

  * مدیریت شبکه Podها
  * اعمال NetworkPolicy و امنیت شبکه
  * مسیریابی با BGP و IPAM
    را انجام می‌دهد.
* نقش Calico: از policyهای Kubernetes و CRDهای خودش استفاده می‌کند تا ترافیک شبکه را کنترل و امن کند.

---

## ۲. اجزای اصلی Calico

| جزء                         | نقش                                              |
| --------------------------- | ------------------------------------------------ |
| **calico-node**             | اجرای مسیریابی و اعمال policy روی هر Node        |
| **calico-typha**            | هماهنگی بین nodeها برای کاهش فشار روی API Server |
| **calico-kube-controllers** | سینک داده‌ها با Kubernetes و etcd                |
| **csi-node-driver**         | مربوط به volume و storage (نه بخش اصلی Calico)   |

> همه در namespace `calico-system` اجرا می‌شوند و وضعیت `Running` دارند.

---

## ۳. منابع پیکربندی

| نوع       | نام                | توضیح                              |
| --------- | ------------------ | ---------------------------------- |
| ConfigMap | `cni-config`       | تنظیمات CNI و اتصال Podها          |
| ConfigMap | `tigera-ca-bundle` | گواهی TLS برای اجزای Calico/Tigera |
| Secret    | `node-certs`       | TLS برای calico-node               |
| Secret    | `typha-certs`      | TLS برای Typha                     |

> این منابع باید با شبکه و policyهای کلاستر سازگار باشند.

---

## ۴. CRDهای Calico

Calico از CRDهای خاص برای مدیریت ویژگی‌های پیشرفته استفاده می‌کند:

* **BGP و شبکه**

  * `bgpconfigurations`
  * `bgppeers`
  * `ippools`
* **NetworkPolicy و امنیت**

  * `networkpolicies`
  * `globalnetworkpolicies`
  * `networksets`
* **Node و Felix**

  * `felixconfigurations`
  * `hostendpoints`
  * `caliconodestatuses`
* **سایر**

  * `clusterinformations`
  * `tiers`

---

## ۵. NetworkPolicy در Calico

* `NetworkPolicy` استاندارد Kubernetes است که مشخص می‌کند **کدام Podها می‌توانند با هم ارتباط داشته باشند**.
* بدون CNI مانند Calico، policyها اجرایی نمی‌شوند.
* Calico policyها را به **iptables یا eBPF** ترجمه و اجرا می‌کند.
* مثال:

  ```text
  akherati: argocd-repo-server-network-policy
  ```

  فقط اجازه می‌دهد Podهای مشخص به سرویس ArgoCD متصل شوند.

### 🔹 GlobalNetworkPolicy

* سیاست‌های سطح کل کلاستر
* فقط توسط Calico اعمال می‌شود، نه Kubernetes استاندارد.

---

## ۶. بررسی و استخراج تنظیمات

### 🟢 منابع جاری Calico:

```bash
kubectl get ippools,felixconfigurations,bgpconfigurations,networkpolicies,hostendpoints -A
```

### 🟢 استخراج کامل YAML:

```bash
kubectl get ippools -A -o yaml > ippools.yaml
kubectl get felixconfigurations -A -o yaml > felix.yaml
kubectl get bgpconfigurations -A -o yaml > bgp.yaml
kubectl get bgppeers -A -o yaml > bgppeers.yaml
kubectl get networkpolicies -A -o yaml > networkpolicies.yaml
kubectl get globalnetworkpolicies -A -o yaml > globalnetworkpolicies.yaml
kubectl get hostendpoints -A -o yaml > hostendpoints.yaml
kubectl get clusterinformations -A -o yaml > clusterinfo.yaml
```

> این فایل‌ها برای مستندسازی و تحلیل امنیتی قابل استفاده هستند.

---

## ۷. نکات مهم

1. Calico هم NetworkPolicyهای Kubernetes و هم CRDهای خودش را مدیریت می‌کند.
2. اجزای اصلی در `calico-system` اجرا می‌شوند و باید همیشه `Running` باشند.
3. Policyها به صورت namespace-specific یا global قابل تعریف هستند.
4. IPAM و BGP از ویژگی‌های کلیدی Calico برای مسیریابی Podها و Nodeها هستند.
5. بررسی کامل شامل Pods، Services، ConfigMaps، Secrets و CRDهای Calico است.

---

اگر بخوای، می‌تونم همین جزوه رو **به صورت یک فایل PDF آماده و مرتب** کنم تا مستقیم ذخیره و چاپش کنی.

می‌خوای این کارو انجام بدم؟
