# kubectl top
دستور `kubectl top` در Kubernetes به منظور نمایش اطلاعات مربوط به مصرف منابع توسط اجزاء مختلف مانند پادها (Pods) و نودها (Nodes) استفاده می‌شود. این دستور به صورت زیر عمل می‌کند:

**نمایش مصرف منابع پادها:**
   ```bash
   kubectl top pods
   ```

   این دستور اطلاعات مصرف CPU و حافظه تمام پادها را نمایش می‌دهد.

**نمایش مصرف منابع نودها:**
   ```bash
   kubectl top nodes
   ```

   این دستور اطلاعات مصرف منابع CPU و حافظه تمام نودها را نمایش می‌دهد.

**نمایش مصرف منابع یک پاد خاص:**
   ```bash
   kubectl top pod <pod-name>
   ```

   این دستور اطلاعات مصرف CPU و حافظه برای یک پاد خاص را نمایش می‌دهد.

در هر یک از دستورات بالا، اطلاعات مصرف منابع به صورت زمان‌بندی شده نمایش داده می‌شوند. این اطلاعات می‌توانند به شما کمک کنند تا مصرف منابع در کلستر Kubernetes خود را مدیریت و بهینه‌سازی کنید.

لطفاً دقت کنید که برای استفاده از `kubectl top`، Metric Server باید در کلستر Kubernetes شما نصب و در حال اجرا باشد. Metric Server یک کامپوننت است که اطلاعات مربوط به مصرف منابع را جمع‌آوری و ارائه می‌دهد. اگر Metric Server نصب نیست، شما باید ابتدا آن را نصب کنید.

برای نصب Metric Server، می‌توانید از دستورات مختلفی که بسته به نوع کلاستر و استفاده از ابزار‌های مدیریت کلاستر (مانند kubeadm یا Helm) متفاوت استفاده کنید.