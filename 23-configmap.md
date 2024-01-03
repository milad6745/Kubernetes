## `create POD with Config MAP`


برای ایجاد یک ConfigMap که حاوی فایل پیکربندی Nginx با نام `nginx.conf` باشد و سپس استفاده از آن در یک پاد جدید، می‌توانید مراحل زیر را دنبال کنید:

1. **ساخت فایل `nginx.conf`:**
   ابتدا فایل پیکربندی Nginx به نام `nginx.conf` را ایجاد کنید. مثالی از یک فایل `nginx.conf` ساده ممکن است به شکل زیر باشد:

   ```nginx
   server {
       listen 80;
       server_name mynginx;
       location / {
           root /usr/share/nginx/html;
           index index.html;
       }
   }
   ```

2. **ایجاد ConfigMap:**
   حالا از فایل `nginx.conf` یک ConfigMap ایجاد کنید. این دستور به فرض استفاده از فایل `nginx.conf` که در همان دایرکتوری اجرایی قرار دارد:

   ```bash
   kubectl create configmap nginx-config --from-file=nginx.conf
   ```

   در اینجا، `nginx-config` نام ConfigMap است و `--from-file=nginx.conf` نشان‌دهنده این است که اطلاعات ConfigMap از محتوای فایل `nginx.conf` استخراج شده‌اند.

3. **استفاده از ConfigMap در یک پاد:**
   سپس، یک فایل YAML برای تعریف یک پاد با استفاده از ConfigMap ایجاد کنید. به عنوان مثال:

   ```yaml
   apiVersion: v1
   kind: Pod
   metadata:
     name: nginx-pod
   spec:
     containers:
     - name: nginx-container
       image: nginx
       volumeMounts:
       - name: nginx-config-volume
         mountPath: /etc/nginx/conf.d
     volumes:
     - name: nginx-config-volume
       configMap:
         name: nginx-config
   ```

   در این مثال:
   - یک پاد با نام `nginx-pod` ایجاد شده است.
   - یک کانتینر Nginx با نام `nginx-container` استفاده شده است.
   - یک Volume به نام `nginx-config-volume` ایجاد شده است که به ConfigMap با نام `nginx-config` متصل شده است.
   - اطلاعات ConfigMap در مسیر `/etc/nginx/conf.d` در کانتینر متصل می‌شوند.

4. **اجرای پاد:**
   سپس فایل YAML را به کمک دستور `kubectl apply` اجرا کنید:

   ```bash
   kubectl apply -f your-pod-file.yaml
   ```

   این دستور پاد را با تنظیمات ConfigMap ایجاد می‌کند و در حال اجرا قرار می‌دهد.

حالا شما یک ConfigMap با نام `nginx-config` دارای فایل پیکربندی `nginx.conf` هستید و این ConfigMap در یک پاد با نام `nginx-pod` استفاده شده است. تغییرات در فایل پیکربندی Nginx با تغییر در ConfigMap بدون نیاز به تغییر در پادها اعمال می‌شود، که این امکان را به شما می‌دهد تا پیکربندی‌های خود را به راحتی مدیریت کنید.
