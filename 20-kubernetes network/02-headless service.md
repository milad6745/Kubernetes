# headless service

در Kubernetes، Headless Service یک نوع خاص از خدمات (Services) است که برخلاف خدمات معمولی، به پادها (Pods) IP ثابت نمی‌دهد. Headless Service برای تعریف یک محیط دیگر به نام "خدمات بی‌سر" (Headless Services) استفاده می‌شود.

ویژگی اصلی Headless Service این است که IP‌ها به صورت دینامیک تولید می‌شوند و نام DNS به آن‌ها تعلق دارد. به عبارت دیگر، برای هر پاد یک رکورد DNS ایجاد می‌شود و از طریق این رکورد DNS به آن پاد دسترسی پیدا می‌کنید. این به شما امکان می‌دهد که به‌سرعت به هر پاد در محیط شبکه Kubernetes دسترسی پیدا کنید.

برای تعریف یک Headless Service، شما باید فیلد `clusterIP` را با مقدار `None` تنظیم کنید. مثال:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-headless-service
spec:
  clusterIP: None
  selector:
    app: my-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8080
```

در این مثال، `clusterIP: None` نشان‌دهنده این است که این Headless Service هیچ IP ثابتی ندارد. سپس، بر اساس مقادیر مشخص شده در `selector`، به هر پاد متناظر با این انتخاب‌گر یک رکورد DNS تخصیص می‌یابد.

استفاده اصلی از Headless Service زمانی است که شما به دلیل نیازهای خاص نیاز به آدرس‌های IP ثابت برای هر پاد ندارید و از DNS برای دسترسی به پادها استفاده می‌کنید.
