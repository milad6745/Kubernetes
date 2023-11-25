# Deployment Strategy
استراتژی هایی که در آپدیت کردن Deployment ها انجام میدهیم .


در Kubernetes، Deployment Strategy یکی از اجزای مهم است که به شما امکان می‌دهد که به صورت کنترل شده و با انعطاف‌پذیری بالا، اپلیکیشن‌ها را به‌روز رسانی (update) یا افزایش مقیاس (scale) دهید. استراتژی‌های مختلفی برای اجرای به‌روزرسانی در Kubernetes وجود دارد. برخی از استراتژی‌های Deployment عبارتند از:

**Recreate (بازسازی):**


   در این استراتژی، تمام Pod های قدیمی حذف شده و Pod های جدید با تغییرات جدید ایجاد می‌شوند. این روش ساده است اما ممکن است زمان بیشتری برای به‌روزرسانی بنا به تعداد Replica های Deployment طول بکشد. همچنین، ممکن است در حین به‌روزرسانی،  دان تایم بدهیم.

![image](https://github.com/milad6745/Kubernetes/assets/113288076/01b8b57a-943e-48e7-b69f-8f2cbe0ba6aa)

   

**RollingUpdate (به‌روزرسانی پیش‌رونده):**


   این استراتژی یک نسخه جدید از Pod ها را به تدریج جایگزین می‌کند. تعداد Replica های جدید تدریجاً افزایش می‌یابد و تعداد Replica های قدیمی کاهش می‌یابد، تا زمانی که تمامی Replica ها به نسخه جدید به‌روزرسانی شوند. این روش یک توازن خوب بین دردسر ناپذیری و استفاده بهینه از منابع فراهم می‌کند.

![image](https://github.com/milad6745/Kubernetes/assets/113288076/5cdcb529-ecf3-4c90-b25c-fd62d48bd2fc)

   

**Blue-Green Deployment (نصب آبی-سبز):**


   در این استراتژی، دو محیط (آبی و سبز) وجود دارد. محیط آبی اپلیکیشن فعلی است و محیط سبز جایگزین جدید است. ابتدا ترافیک به محیط آبی هدایت می‌شود. سپس، پس از ایجاد و تست کامل در محیط سبز، ترافیک به محیط سبز هدایت می‌شود. این استراتژی امکان به‌روزرسانی به صورت فوری و با کمترین احتمال اختلال در دسترسی به سرویس را فراهم می‌کند. این استراتژی به دلیل استفاده از منابع زیادتر پر هزینه است .
   

![image](https://github.com/milad6745/Kubernetes/assets/113288076/6a3004cc-9cff-47da-b10b-19a2f073d8a5)



**Canary Deployment (نصب کانری):**


   در این استراتژی، یک تعداد کم از تغییرات (کانری) به تدریج در محیط Production اعمال می‌شود و ترافیک به آن تغییرات هدایت می‌شود. این به اپراتورها امکان می‌دهد تا تاثیر تغییرات را بر روی محیط Production بررسی کرده و در صورت مواجهه با مشکلات، به سرعت از تغییرات عقب بروند.

هر استراتژی به نیازها و شرایط خاص خود می‌پردازد و انتخاب یک استراتژی به میزان ویژگی‌ها و نیازهای اپلیکیشن شما بستگی دارد.
مقداری از ترافیک را سمت اپلیکیشن جدید میفرستیم اگر همه چیز اکی بود کمکم کل ترافیک را روشی جدید میریزیم .


![image](https://github.com/milad6745/Kubernetes/assets/113288076/f8a132e1-09a9-453e-a28f-1ff5860b311c)


در یک نگاه : 
![image](https://github.com/milad6745/Kubernetes/assets/113288076/18b367cf-20e6-44ae-b3ee-1fe1ffa08d42)



# Recreate Example
در اینجا ما با استفاده از استراتژی Recreate دو نود را بالا میاوریم و در صورتی که آپدیتی مثلا در image داشته باشیم مشاهده میشود که تمامی نو هایمان دان میشوند و دوباره ایجاد میشوند . 
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: recreate
  annotations:
    kubernetes.io/change-cause: ver1
spec:
  selector:
    matchLabels:
      app: recreate
  replicas: 10
  strategy:
    type: Recreate
  template:
    metadata:
      labels:
        app: recreate
        svc: example
    spec:
      containers:
        - name: hello-world
          image: ahmadrafiee/go-hello-world:1
          ports:
            - containerPort: 80
          resources:
            requests:
              cpu: 10m
              memory: 10Mi
            limits:
              memory: 20Mi
              cpu: 20m
---
apiVersion: v1
kind: Service
metadata:
  name: recreate
spec:
  type: NodePort
  ports:
    - port: 80
      protocol: TCP
      targetPort: 80
  selector:
    app: recreate
```
```
kubectl get deployments.apps -o wide
NAME          READY   UP-TO-DATE   AVAILABLE   AGE    CONTAINERS    IMAGES                         SELECTOR
recreate      10/10   10           10          55s    hello-world   ahmadrafiee/go-hello-world:1   app=recreate
```
لحظه دان شدن تمامی نود ها در Deployment امان در زمان تغییر در ایمیج

![image](https://github.com/milad6745/Kubernetes/assets/113288076/83f40311-e1d6-4c07-8b68-e2dbdc0ab235)

![image](https://github.com/milad6745/Kubernetes/assets/113288076/af06be90-c0dc-4da6-b253-36ed4908910f)


