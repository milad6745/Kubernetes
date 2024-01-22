##`Resource Management`
در این شکل نشان میدهد که ما نبیاد تمامی منابعمان را به پاد ها اختصاص دهیم .
مقداری برای سیستم عامل - مقداری برای kubelete و مقداری برای پاد ها و مقداری هم برای Eviction

![image](https://github.com/milad6745/Kubernetes/assets/113288076/28e51eaa-4377-4713-9f7e-0f6848126ac5)

در این شکل هم نشان میدهد که اگر تقریبن 64 کور CPU داریم 63 کور به پاد ها و کمتر از یک کور را  برای موارد دیگر در نطر بگیریم.

![image](https://github.com/milad6745/Kubernetes/assets/113288076/f05c49ed-77b2-4301-9565-388674c3d4ae)

در کل در یک نگاه میزان منابعی که بر میدارد برای غیر از استفاده پاد ها خیلی زیاد نیست
![image](https://github.com/milad6745/Kubernetes/assets/113288076/5ca81fee-aea7-4bdd-bbbb-926092a324ec)



تمامی کانفیگ ها با این چند خط در Kuebelete انجام میشود .

`Node has 32Gi of memory, 16 CPUs and 100Gi of Storage`
```
--kube-reserved is set to cpu=1,memory=2Gi, ephemeral-storage=1Gi
--system-reserved is set to cpu=500m,memory=1Gi, ephemeral-storage=1Gi
--eviction-hard is set to memory.available<500Mi, nodefs.available<10%
```
