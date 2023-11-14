# path
مسیر تمامی manifest ها در کلاستر کوبر بصورت زیر است .
```bash
#/etc/kubernetes/manifests# ls
etcd.yaml  kube-apiserve.yaml  kube-controller-manager.yaml  kube-scheduler.yaml
```

اگر فایل apiserver را تغییر دهیم یا پاک کنیم دیگر کسی نیست که به ما جواب دهد و ارتباط با کوبرنتیز قطع میشود .
```bash
# mv kube-apiserver.yaml /opt/
# kubectl get pod -A
The connection to the server milad-cluster-control-plane:6443 was refused - did you specify the right host or port?
```

