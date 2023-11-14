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
