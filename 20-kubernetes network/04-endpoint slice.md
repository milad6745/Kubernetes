# Endpoint - Endpoint Slice

پشت هر سرویس یک سری پاد هستند لیست پار ها در End point ذخیره میشود.
سرویس Endpoint Slice جدیدتر از Endpoint است و داره جایگزین میشه .

```
kubectl get endpoints
NAME                  ENDPOINTS         AGE
kubernetes            172.18.0.3:6443   7d1h

kubectl get endpointslices.discovery.k8s.io
NAME                        ADDRESSTYPE   PORTS     ENDPOINTS    AGE
kubernetes                  IPv4          6443      172.18.0.3   7d1h
```
الان یک پاد در این سرویس kubernetes است و به محض اضافه شدن Replica در روی سرویس تعداد Endpoint ها افزایش میابد .


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

برای در آوردن yaml فایل End point هایمان:
```
 kubectl get endpointslices.discovery.k8s.io kubernetes -o yaml
addressType: IPv4
apiVersion: discovery.k8s.io/v1
endpoints:
- addresses:
  - 172.18.0.3
  conditions:
    ready: true
kind: EndpointSlice
metadata:
  creationTimestamp: "2023-11-26T17:55:45Z"
  generation: 1
  labels:
    kubernetes.io/service-name: kubernetes
  name: kubernetes
  namespace: default
  resourceVersion: "196"
  uid: 270a8473-cfc6-4746-85bc-17b4de5e8a60
ports:
- name: https
  port: 6443
  protocol: TCP
```
