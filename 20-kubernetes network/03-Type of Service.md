# Type of Service

- cluster ip
- node port
- Load balancer
- External name

  هر 4 سرویس به ترتیب وقتی جلو میرود فانکشنالیتی سرویس قبلی را دارد .


  node port = clusterip + ...
  
  load balancer = nodeport + ...
  
  external name = load balancer + ...
  
  
  
![image](https://github.com/milad6745/Kubernetes/assets/113288076/b91acb6b-5b0b-4f6f-89f1-8a236053f2c7)



# cluster ip

یک سرویسی است که یه اینترنال آیپی را لیسن میکنه و بهش میگن که داخل کوبر این آیپی استفاده میشود .
یه وب سرویس داریم میخواهیم با دیتابیس داخلیش صحبت کنه

![image](https://github.com/milad6745/Kubernetes/assets/113288076/cdc28d43-8a59-46ca-83c1-8250b3813ecc)


در Kubernetes، ClusterIP و NodePort دو نوع خدمات (Service) هستند که امکان دسترسی به پادها (Pods) درون کلاستر Kubernetes را فراهم می‌کنند. در زیر شرح هرکدام ارائه شده است:

## 1. ClusterIP:

ClusterIP 

یک نوع خدمت است که به هر پاد درون کلاستر Kubernetes یک آدرس IP داخلی (internal IP) اختصاص می‌دهد. این IP فقط درون کلاستر قابل دسترسی است و از خارج کلاستر قابل دسترسی نیست. 

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-clusterip-service
spec:
  selector:
    app: my-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8080
```

در این مثال، `my-clusterip-service` یک ClusterIP Service است که به هر پاد با label `app: my-app` یک IP داخلی اختصاص می‌دهد. این IP می‌تواند از سایر پادها درون کلاستر برای ارتباط استفاده شود.

## 2. NodePort:

NodePort 



نیز یک نوع خدمت است که به هر پاد یک آدرس IP و پورت درون کل نودهای کلاستر (Node) اختصاص می‌دهد. این آدرس IP و پورت از هر نودی درون کلاستر قابل دسترسی است. در صورت فعالسازی بر روی تمامی نود های کلاستر آن پورت باز میشود .
کلا مدلی هست که توصیه نمیشود .



![image](https://github.com/milad6745/Kubernetes/assets/113288076/4712f999-efc3-4097-9141-0b082fa856ae)


```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-nodeport-service
spec:
  type: NodePort
  selector:
    app: my-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8080
```
```
kubectl get svc
NAME                  TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
kubernetes            ClusterIP   10.96.0.1       <none>        443/TCP        6d1h
my-nodeport-service   NodePort    10.96.227.182   <none>        80:32335/TCP   8s
```

pod port = target port

service port = port

node port = node port به پورتی که روز پاد لیستن میشه میگن نود پورت



![image](https://github.com/milad6745/Kubernetes/assets/113288076/354626df-2dea-4e97-9aa3-c58d8d5a8ea1)

در این مثال، `my-nodeport-service` یک NodePort Service است که به هر پاد با label `app: my-app` یک IP و پورت درون کل نودهای کلاستر اختصاص می‌دهد. این IP و پورت از هر نود درون کلاستر (با توجه به نود‌های کلاستر) قابل دسترسی است و می‌توانید از طریق آن به خدمت دسترسی پیدا کنید.

با استفاده از ClusterIP و NodePort می‌توانید به پادهای درون کلاستر Kubernetes دسترسی یافته و خدمات مختلف را بر روی آنها ارائه دهید.

## Loadbalancer

در Kubernetes، خدمات LoadBalancer یک نوع خدمت (Service) هستند که به شما امکان می‌دهند تا یک Load Balancer خارجی (external Load Balancer) برای دسترسی به پادها (Pods) درون کلاستر ایجاد کنید. این خدمت‌ها به طور خودکار یک Load Balancer خارجی (مثل Load Balancer مربوط به ارائه دهنده ابری یا Load Balancer‌های فیزیکی) ایجاد می‌کنند و درخواست‌های خارجی را به پادهای درون کلاستر توزیع می‌کنند.
برای این منظور نیاز به آیپی ولید و یک پروایدر اینترنی است .

برای ایجاد یک خدمت LoadBalancer در Kubernetes، می‌توانید از مانیفست YAML زیر استفاده کنید:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-loadbalancer-service
spec:
  type: LoadBalancer
  selector:
    app: my-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8080
```

در این مثال:

- `type: LoadBalancer` نشان‌دهنده نوع خدمت استفاده شده است.
- `selector` با استفاده از برچسب‌ها (labels)، مشخص می‌کند که این خدمت به کدام پادها مرتبط است.
- `ports` مشخص می‌کند که چه پورت‌هایی برای خدمت LoadBalancer باز شود و به چه پورت‌هایی درون پادها توجه شود.

بعد از اجرای این مانیفست، Kubernetes تلاش می‌کند تا یک Load Balancer خارجی را برای شما ایجاد کند و ترافیک خارجی را به پادهای مشخص‌شده توزیع کند. لازم به ذکر است که این قابلیت فقط در محیط‌هایی که از این ویژگی پشتیبانی می‌کنند (مانند محیط‌های ابری) ممکن است.
