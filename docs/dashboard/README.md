### Installing kubernetes dashboard:
---------------------------------------------------------
Run the below command.

```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.0/aio/deploy/recommended.yaml
```

Dashboard can be access in two ways.

1. port-forward -> kubectl -n <namespace> port-forward <dashboard pod> <localport>:<pod target port>
2. Editing the dashboard service i.e changing to Nodeport

Now the dashboard can be accessible in any of the methods described above. However we can not login and view the objects.
to do so.

Create a file with below content and run kubectl apply against that file.

```
apiVersion: v1
kind: ServiceAccount
metadata:
  name: dashboard-admin
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: cluster-admin-rolebinding
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: dashboard-admin
  namespace: kube-system
```

new service account "dashboard-admin" gets created. This inturn creates a secret, Run kubectl describe on that secret and get the value.
Copy the Secret Value in kubernetes login page(Access method - token)
