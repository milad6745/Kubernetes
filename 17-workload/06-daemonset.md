# daemonset
یک ورک لود است که نیاز دارید که به ازای همه نود ها چیزی بالا بیاید .
در تمام نود ها چیزی بالا میاید و در صورت حذف نود daemonset هم کم میشود.
مثلا ایجینت مانیتورینگ برای نود هایمان.
موارد استفاده :
ایجینت مانیتورینگ- کلاستر استوریج - لاگ کالکشن 

```
kubectl get daemonset -A
NAMESPACE     NAME         DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR            AGE
kube-system   kindnet      1         1         1       1            1           <none>                   14d
kube-system   kube-proxy   1         1         1       1            1           kubernetes.io/os=linux   14d
```
این دو در هر نود بالا میاید . عدد 1 نشان میدهد که ما یک نود داریم .

## static pod vs daemon set

استاتیک پاد توسط kubelet  دپلوی میشه و نیاز به api server ندارد .
اما Daemon set توسظ kube-api ساخته میشود .
هر دو kube scheduler را ignore میکنند .

