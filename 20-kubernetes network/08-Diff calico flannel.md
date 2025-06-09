## Diff calico flanne

شبکه‌های Overlay مانند Calico و Flannel برای فراهم کردن ارتباط بین Pods در کلاستر Kubernetes، به خصوص زمانی که Pods روی Nodeهای مختلف در کلاستر اجرا می‌شوند، استفاده می‌شوند. این شبکه‌ها یک لایه مجازی از شبکه‌های اختصاصی را بین Pods ایجاد می‌کنند.

Flannel:

یک راه‌حل ساده برای شبکه‌بندی Overlay است که بسته‌ها را از طریق یک شبکه مجازی از انواع مختلف (VXLAN، host-gw و...) منتقل می‌کند.
Calico: 

علاوه بر فراهم کردن شبکه Overlay، به عنوان یک راه‌حل امنیتی پیشرفته نیز شناخته می‌شود که امکاناتی مانند Network Policies را برای کنترل دسترسی بین Pods فراهم می‌آورد.



## خلاصه نقش Calico:

Calico یک CNI (Container Network Interface) plugin است که دو نقش اصلی دارد:

- شبکه‌سازی بین پادها (Pod Networking)

- اعمال سیاست‌های امنیتی شبکه (NetworkPolicy Enforcement)
