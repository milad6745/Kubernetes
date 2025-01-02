وقتی از **Ingress** در Kubernetes استفاده می‌کنید، مسیر جریان پکت‌ها از خارج از کلاستر تا رسیدن به Pod به صورت زیر است:

---

### مراحل جریان ترافیک:

1. **ورود ترافیک از خارج (Client → Cluster):**
   - یک درخواست HTTP/S از کلاینت ارسال می‌شود.
   - این درخواست به آدرس IP عمومی یا DNS متصل به Load Balancer (در صورت استفاده) می‌رسد. این Load Balancer معمولاً توسط یک سرویس نوع **LoadBalancer** در Kubernetes ایجاد می‌شود.

2. **Load Balancer → Ingress Controller:**

   - Load Balancer درخواست را به نودهایی که Ingress Controller روی آن‌ها در حال اجرا است، هدایت می‌کند.
   - Ingress Controller یک Pod در کلاستر Kubernetes است که درخواست‌ها را بر اساس قوانین تعریف‌شده در منابع **Ingress** مدیریت می‌کند.

3. **Ingress Controller → Service:**
   
   - Ingress Controller درخواست را بررسی کرده و مسیر آن را طبق قوانین (مانند `host` یا `path`) به **Service** مناسب در کلاستر هدایت می‌کند.
   - Service معمولاً نوع **ClusterIP** دارد و درخواست‌ها را به یکی از Podهای پشت آن Service ارسال می‌کند.

4. **Service → Pod:**
   
   - Service به‌عنوان یک Load Balancer داخلی عمل می‌کند و ترافیک ورودی را به یکی از Podهای پشت آن هدایت می‌کند.
  
```
Client (کاربر خارجی)
    |
    v
Load Balancer (در صورت استفاده، IP عمومی یا DNS)
    |
    v
Ingress Controller (مانند NGINX Ingress یا Traefik)
    |
    v
Service (ClusterIP یا NodePort)
    |
    v
Pod (نهایی، درخواست به کانتینر می‌رسد)
```
