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

ClusterIP یک نوع خدمت است که به هر پاد درون کلاستر Kubernetes یک آدرس IP داخلی (internal IP) اختصاص می‌دهد. این IP فقط درون کلاستر قابل دسترسی است و از خارج کلاستر قابل دسترسی نیست. 

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

NodePort نیز یک نوع خدمت است که به هر پاد یک آدرس IP و پورت درون کل نودهای کلاستر (Node) اختصاص می‌دهد. این آدرس IP و پورت از هر نودی درون کلاستر قابل دسترسی است. 

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

در این مثال، `my-nodeport-service` یک NodePort Service است که به هر پاد با label `app: my-app` یک IP و پورت درون کل نودهای کلاستر اختصاص می‌دهد. این IP و پورت از هر نود درون کلاستر (با توجه به نود‌های کلاستر) قابل دسترسی است و می‌توانید از طریق آن به خدمت دسترسی پیدا کنید.

با استفاده از ClusterIP و NodePort می‌توانید به پادهای درون کلاستر Kubernetes دسترسی یافته و خدمات مختلف را بر روی آنها ارائه دهید.
