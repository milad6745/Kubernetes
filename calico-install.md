
### Before you begin
Ensure Docker is installed
Install, but do not create cluster, kind. How to install Kind
Install kubectl.How to install kubectl
How to
Create a multi-node kind cluster
KIND ships with a simple networking implementation ("kindnetd") based around standard CNI plugins (ptp, host-local, …) and simple netlink routes. You need to disable the default CNI to use Calico CNI.

Kind cluster creation can be modified by configuration file. Here you are providing kind configuration to disable simple networking implementation (default CNI) and change the pod ip CIDR.

Disable the default CNI to use Calico CNI by running the following command:
```
cat > values.yaml <<EOF
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
- role: worker
- role: worker
networking:
  disableDefaultCNI: true
  podSubnet: 192.168.0.0/16
EOF
```

Start your Kind cluster with one control plane and two worker nodes by running the following command:
```
kind create cluster --config values.yaml --name dev
```
Confirm that you now have three nodes in your cluster by running the following command:
```
kubectl get nodes -o wide
```
It should return something like the following.
```
NAME                STATUS      ROLES           AGE    VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION    CONTAINER-RUNTIME
dev-control-plane   NotReady    control-plane   4m     v1.25.0   172.18.0.2    <none>        Ubuntu 22.04.1 LTS   5.10.0-17-amd64   containerd://1.6.7
dev-worker          NotReady    <none>          4m     v1.25.0   172.18.0.4    <none>        Ubuntu 22.04.1 LTS   5.10.0-17-amd64   containerd://1.6.7
dev-worker2         NotReady    <none>          4m     v1.25.0   172.18.0.3    <none>        Ubuntu 22.04.1 LTS   5.10.0-17-amd64   containerd://1.6.7
```

Install Calico
Operator
Manifest
Install Calico by using the following command.

```
kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.29.3/manifests/calico.yaml
```
note
Due to the large size of the CRD bundle, kubectl apply might exceed request limits. Instead, use kubectl create or kubectl replace.

note
You can also view the YAML in a new tab.

You should see the following output.
```
configmap/calico-config created
customresourcedefinition.apiextensions.k8s.io/bgpconfigurations.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/bgppeers.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/blockaffinities.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/clusterinformations.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/felixconfigurations.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/globalnetworkpolicies.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/globalnetworksets.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/hostendpoints.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/ipamblocks.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/ipamconfigs.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/ipamhandles.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/ippools.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/kubecontrollersconfigurations.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/networkpolicies.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/networksets.crd.projectcalico.org created
clusterrole.rbac.authorization.k8s.io/calico-kube-controllers created
clusterrolebinding.rbac.authorization.k8s.io/calico-kube-controllers created
clusterrole.rbac.authorization.k8s.io/calico-node created
clusterrolebinding.rbac.authorization.k8s.io/calico-node created
daemonset.apps/calico-node created
serviceaccount/calico-node created
deployment.apps/calico-kube-controllers created
serviceaccount/calico-kube-controllers created
```
Verify Calico installation
You can verify Calico installation in your cluster by issuing the following command.

watch kubectl get pods -l k8s-app=calico-node -A

You should see a result similar to the below. Note that the namespace might be different, depending on the method you followed.
```
NAMESPACE     NAME                READY   STATUS    RESTARTS   AGE
kube-system   calico-node-2xcf4   1/1     Running   0          57s
kube-system   calico-node-gkqkg   1/1     Running   0          57s
kube-system   calico-node-j44hp   1/1     Running   0          57s
```
