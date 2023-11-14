# practice

1- edit replica core dns from 2 to 4

2- delete a core dns pod and check count of pod coredns


## edit coredns config
```bash
kubectl edit  deployment.app -n kube-system coredn

spec:
  progressDeadlineSeconds: 600
  replicas: 2 --> 4
```

## check
```bash
# kubectl get pod -n kube-system | grep -i  dns     
coredns-565d847f94-hgzvj                              1/1     Running   0             2m19s
coredns-565d847f94-nm58l                              1/1     Running   0             47h
coredns-565d847f94-vr9f4                              1/1     Running   0             2m44s
coredns-565d847f94-xrp5n                              1/1     Running   0             2m44s
```

## delete a core dns pod

```bash
# kubectl delete pod coredns-565d847f94-vr9f4 -n kube-system

kubectl get pod -n kube-system | grep -i  dns       با زدن این کامند یک پاد به مد ترمینیت میرود . و پاد دیگری ساخته میشود
coredns-565d847f94-hgzvj                              1/1     Running       0             4m22s
coredns-565d847f94-j624g                              1/1     Running       0             3s
coredns-565d847f94-nm58l                              1/1     Running       0             47h
coredns-565d847f94-vr9f4                              1/1     Terminating   0             4m47s
coredns-565d847f94-xrp5n                              1/1     Running       0             4m47s
```
این وظیفه کنترل منیجر است و در صورتی که آن را دان کنیم یا دان بشود دیگر این ساخت پاد جدید اتفاق نمیافتد.
