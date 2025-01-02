
## Create read only user on mon namespace

میخواهمی به کاربر ahmad  دسترسی فقط خواندنی در namespace mon  بدهیم

**Step a:** create namespace
```bash
kubectl create namespace mon
```

**Step 2:** create service account
```bash
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: ServiceAccount
metadata:
  name: ahmad
  namespace: mon
EOF
```

**Step 2:** create role
api groups: all
resource: all
verb: get, watch, list
```bash
cat <<EOF | kubectl apply -f -
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: mon
  name: reader
rules:
- apiGroups: ["*"] # "" indicates the core API group
  resources: ["*"]
  verbs: ["get", "watch", "list"]
EOF
```

**Step 3:** create cluster role binding
cluster role: reader
service account: ahmad
```bash
cat <<EOF | kubectl apply -f -
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: reader
  namespace: mon
roleRef:
  kind: Role
  name: reader
  apiGroup: rbac.authorization.k8s.io
subjects:
- kind: ServiceAccount
  name: ahmad
  namespace: mon
EOF
```

**Step 4:** get token for ahmad service account
```bash
kubectl -n mon create token ahmad
eyJhbGciOiJSUzI1NiIsImtpZCI6IlVoa3VJTjlUa29ZRXd2cHJaamM2ejhnaThmUWFicE9zUDVrZHZIY0VqUncifQ.eyJhdWQiOlsiaHR0cHM6Ly9rdWJlcm5ldGVzLmRlZmF1bHQuc3ZjLmNsdXN0ZXIubG9jYWwiXSwiZXhwIjoxNzM1ODMwODEwLCJpYXQiOjE3MzU4MjcyMTAsImlzcyI6ImhvfRYyC-81XyZCyzSNhqhnqpPN40dfuqPg9ctjkj-KBEPmPAB37xH0QTkB62vnpHh2-K6duEkp89UKrombGJhzTNqXFEo0jrfAoi2_OO4YOGpnwjgc6HW2x8n_ZM8UhsZ-qjTZiYXiS_iBOvKzD3muUhevLutPOFjnGGNtBlHeQaLn54FA7Ft9ZlRTXb6rfw0AyI-qHsAA

```


**step 5:** change kube config به انتها اضافه میکنیم
```bash
- name: ahmad
  user:
    token: eyJhbGciOiJSUzI1NiIsImtpZCI6IlVoa3VJTjlUa29ZRXd2cHJaamM2ejhnaThmUWFicE9zUDVrZHZIY0VqUncifQ.eyJhdWQiOlsiaHR0cHM6Ly9rdWJlcm5ldGVzLmRlZmF1bHQuc3ZjLmNsdXN0ZXIubG9jYWwiXSwiZXhwIjoxNzM1ODMwODEwLCJpYXQiOjE3MzU4MjcyMTAsImlzcyI6Imh0dHBzOi8va3ViZXJuZXRlcy5kZWZhdWx0LnN2Yy5jbHVzdGVyLmxvY2FsqpPN40dfuqPg9ctjkj-KBEPmPAB37xH0QTkB62vnpHh2-K6duEkp89UKrombGJhzTNqXFEo0jrfAoi2_OO4YOGpnwjgc6HW2x8n_ZM8UhsZ-qjTZiYX-iS_iBOvKzD3muUhevLutPOFjnGGNtBlHeQaLn54FA7Ft9ZlRTXb6rfw0AyI-qHsAA
```
and
add ahmad user to context
```bash
- context:
    cluster: kind-kind-kind
    user: ahmad
  name: kind-kind-kind
```


