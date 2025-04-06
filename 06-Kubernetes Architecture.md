# Kubernetes Architecture

![cqud3rjkss361](https://github.com/milad6745/Kubernetes/assets/113288076/1b2825b0-d12f-469f-91ba-796feba9c8c9)

![image](https://github.com/user-attachments/assets/7acdf9cb-8dc7-447d-9b7b-7499a8a8c7ca)


معماری **Kubernetes** (یا به اختصار K8s) یکی از مهم‌ترین مفاهیم در دنیای **DevOps** و **Container Orchestration** هست. در ادامه یک توضیح جامع و قابل فهم از معماری Kubernetes می‌دم:

---

## 🧠 ساختار معماری Kubernetes به زبان ساده:

Kubernetes از **دو بخش اصلی** تشکیل شده:

### 1. **Control Plane (صفحه کنترل)**
### 2. **Nodeها (Worker Nodes)**

---

## ✅ 1. Control Plane (مرکز فرماندهی)
Control Plane مسئول مدیریت کل خوشه (Cluster) هست و تصمیم‌گیری‌های اصلی مثل زمان اجرای کانتینرها، مقیاس‌دهی، به‌روزرسانی، و ... رو انجام می‌ده.

### اجزای اصلی Control Plane:

#### 🌐 API Server
- دروازه اصلی ارتباط با K8s هست.
- همه کامپوننت‌ها از طریق این سرور با K8s ارتباط می‌گیرند.
- RESTful API داره.

#### 🧠 Controller Manager
- وظیفه‌اش پایش و مدیریت وضعیت منابع هست.
- مثلاً وقتی یک Pod خراب میشه، یه Controller تصمیم می‌گیره که یه Pod جدید جایگزین کنه.

#### 📏 Scheduler
- تصمیم می‌گیره که هر Pod روی کدوم Node اجرا بشه.
- بر اساس منابع آزاد، سیاست‌ها و محدودیت‌ها تصمیم‌گیری می‌کنه.

#### 🧠 etcd
- یه دیتابیس کلید-مقدار (key-value) سبک هست.
- همه اطلاعات خوشه اینجا ذخیره می‌شه (شامل وضعیت Podها، ConfigMapها، Secrets و غیره).
- باید **Highly Available** باشه.

---

## ✅ 2. Nodeها (Worker Nodes)
هر Node مثل یه سرور واقعی (یا مجازی) هست که کانتینرها روی اون اجرا می‌شن.

### اجزای اصلی در Node:

#### 🐳 Kubelet
- یه عامل روی هر Node که وضعیت Podها رو کنترل می‌کنه.
- از API Server دستور می‌گیره و اجرایی می‌کنه.

#### 🚢 Kube Proxy
- مسئول شبکه و ارتباط بین سرویس‌ها و Podها هست.
- ترافیک شبکه بین Podها رو مدیریت می‌کنه.

#### 🧱 Container Runtime
- نرم‌افزاری که کانتینرها رو اجرا می‌کنه. (مثلاً Docker، containerd و غیره)

---

## 🖼️ تصویر ذهنی:

```
+------------------+     +------------------+     +------------------+
|   Control Plane  |     |     Worker Node  |     |     Worker Node  |
|------------------|     |------------------|     |------------------|
| - API Server     |<--> | - Kubelet        |     | - Kubelet        |
| - Scheduler      |     | - Kube Proxy     |     | - Kube Proxy     |
| - Controllers    |     | - Container RT   |     | - Container RT   |
| - etcd           |     | - Pods           |     | - Pods           |
+------------------+     +------------------+     +------------------+
```

---

## 💡 نکات تکمیلی:

- ارتباط بین کامپوننت‌ها معمولاً از طریق API Server انجام می‌شه.
- K8s **Self-Healing** هست: اگر چیزی خراب بشه، خودش سعی می‌کنه درستش کنه.
- می‌تونه **Auto-Scaling**، **Load Balancing**، **Rolling Updates** و کلی قابلیت دیگه ارائه بده.

---
