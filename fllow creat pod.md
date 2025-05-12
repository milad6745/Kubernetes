برای ایجاد یک پاد (Pod) در کوبرنتیز (Kubernetes)، یک فلو (Flow) مشخص طی می‌شود. در اینجا یک نمای کلی از فرآیند ایجاد پاد آورده شده است:

🔁 فلو ایجاد یک پاد در کوبرنتیز
تعریف پاد (YAML/CLI)
شما یک شیء پاد را با استفاده از kubectl یا فایل YAML تعریف می‌کنید. مثلاً:

yaml
Copy
Edit
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
  - name: my-container
    image: nginx
ارسال درخواست به API Server
وقتی از kubectl apply یا kubectl create استفاده می‌کنید، دستور به Kubernetes API Server ارسال می‌شود.

احراز هویت و مجوز (Authentication/Authorization)
API Server بررسی می‌کند که کاربر مجاز است این عملیات را انجام دهد یا نه.

ذخیره در etcd
اگر مجاز بود، تعریف پاد در دیتابیس etcd ذخیره می‌شود (پایگاه داده وضعیت خوشه).

Scheduler (زمان‌بند)
کنترلر زمان‌بندی (Scheduler) بررسی می‌کند کدام نود (Node) بهترین گزینه برای اجرای پاد است (با توجه به منابع و قوانین).

ارسال به kubelet
پس از انتخاب نود، kube-apiserver به kubelet (عامل روی نود) اطلاع می‌دهد که پاد را اجرا کند.

اجرای کانتینر توسط container runtime
kubelet از یک runtime مانند containerd یا Docker استفاده می‌کند تا کانتینرهای پاد را راه‌اندازی کند.

گزارش وضعیت
kubelet و سایر کامپوننت‌ها (مثل cAdvisor) وضعیت پاد را به API Server گزارش می‌دهند، که با دستوراتی مانند kubectl get pods قابل مشاهده است.

