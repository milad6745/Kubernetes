 Minikube 

یک ابزار متن باز است که برای ایجاد و مدیریت یک خوشه Kubernetes محلی (local Kubernetes cluster) بر روی یک ماشین مجازی یا سیستم محلی (مثل لپتاپ یا رایانه شخصی) استفاده می‌شود. Minikube یک راه ساده و سریع برای شروع با Kubernetes بر روی یک محیط توسعه‌ای فراهم می‌کند.

ویژگی‌ها و کاربردهای اصلی Minikube عبارتند از:

**استفاده محلی از Kubernetes:** Minikube به شما این امکان را می‌دهد تا یک خوشه Kubernetes کوچک را بر روی ماشین محلی خود اجرا کنید. این کار به شما این امکان را می‌دهد که بدون نیاز به اجرا در یک محیط ابری، کار با Kubernetes را تجربه کنید.

**آموزش و توسعه:** Minikube معمولاً برای آموزش، توسعه و تست برنامه‌های Kubernetes استفاده می‌شود. شما می‌توانید برنامه‌های خود را بر روی Minikube اجرا کنید و به صورت محلی عملیات Kubernetes را تست کنید.

**پشتیبانی از محیط‌های مختلف:** Minikube می‌تواند بر روی محیط‌های مختلفی نظیر VirtualBox، Hyper-V، KVM، VMware، Docker و ... اجرا شود. این امکان به شما این امکان را می‌دهد که Minikube را با توجه به تنظیمات محیط خود اجرا کنید.

**مدیریت تنظیمات مختلف:** Minikube به شما این امکان را می‌دهد که تنظیمات مختلفی نظیر مقداردهی اولیه پارامترها، تغییر محیط اجرا و ... را مدیریت کنید.

برای استفاده از Minikube، باید ابتدا آن را نصب کنید و سپس با استفاده از دستورات مربوطه، یک خوشه Kubernetes محلی را ایجاد کنید.


# Create cluster with minikube
برای ایجاد یک کلاستر Kubernetes با استفاده از Minikube، مراحل زیر را دنبال کنید:

**نصب Minikube:**
   ابتدا اطمینان حاصل کنید که Minikube بر روی سیستم شما نصب شده باشد. شما می‌توانید آخرین نسخه از Minikube را از [وب‌سایت Minikube](https://minikube.sigs.k8s.io/docs/start/) دریافت و نصب کنید.

**نصب یک محیط مجازی (Optional):**
   Minikube نیاز به یک محیط مجازی مانند VirtualBox یا Docker دارد. شما می‌توانید یکی از این محیط‌ها را بر روی سیستم خود نصب کنید. برای نصب VirtualBox یا Docker به [وب‌سایت VirtualBox](https://www.virtualbox.org/) یا [وب‌سایت Docker](https://www.docker.com/) مراجعه کنید.

**اجرای Minikube:**
   از ترمینال یا دستورخط (Command Line) خود Minikube را اجرا کنید. مثلاً اگر از VirtualBox استفاده می‌کنید:

   ```bash
minikube start --driver=virtualbox
minikube start --driver=docker
   ```

   این دستور Minikube را با محیط مجازی VirtualBox راه‌اندازی می‌کند.

**صبر تا Minikube شروع شود:**
   Minikube به مدت چند دقیقه نیاز دارد تا یک کلاستر Kubernetes کوچک را راه‌اندازی کند. صبر کنید تا کلاستر کامل شود.

**احراز هویت با Kubernetes Cluster:**
   بعد از راه‌اندازی، احراز هویت شما با کلاستر باید تنظیم شود. دستور زیر به شما کمک می‌کند تا به کلاستر متصل شوید:

   ```bash
   kubectl config use-context minikube
   ```

   این دستور کانفیگ فعلی `kubectl` را بر روی کلاستر Minikube تنظیم می‌کند.

**بررسی وضعیت کلاستر:**
   حالا می‌توانید وضعیت کلاستر خود را با دستور زیر بررسی کنید:

   ```bash
   kubectl cluster-info
   ```

   این دستور اطلاعات مربوط به کلاستر Kubernetes شما را نمایش می‌دهد.

حالا شما یک کلاستر Kubernetes محلی با استفاده از Minikube دارید که می‌توانید بر روی آن برنامه‌های خود را توسعه دهید و تست کنید.
