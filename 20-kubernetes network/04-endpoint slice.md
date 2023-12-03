در Kubernetes، یک EndpointSlice شامل ارجاع به مجموعه‌ای از نقاط انتهای شبکه (network endpoints) است. سیستم کنترل به صورت خودکار EndpointSlices را برای هر خدمت Kubernetes که یک انتخاب‌گر (selector) مشخص دارد، ایجاد می‌کند. این EndpointSlices ارجاعاتی به تمام پادهایی که با انتخاب‌گر خدمت تطابق دارند را شامل می‌شوند. EndpointSlices نقاط انتهای شبکه را با ترکیب‌های منحصر به فردی از پروتکل، شماره پورت، و نام خدمت گروه‌بندی می‌کنند. نام یک شیء EndpointSlice باید یک نام زیردامنه DNS معتبر باشد.

به عنوان یک مثال، در زیر یک شیء EndpointSlice نمونه آورده شده است که متعلق به یک خدمت Kubernetes نمونه است:

```yaml
apiVersion: discovery.k8s.io/v1
kind: EndpointSlice
metadata:
  name: example-service-xyz
  namespace: default
  labels:
    app: example-app
    tier: backend
    release: "1.0"
addresses:
  - "192.168.1.1"
  - "192.168.1.2"
ports:
  - name: http
    protocol: TCP
    port: 80
  - name: https
    protocol: TCP
    port: 443
```
