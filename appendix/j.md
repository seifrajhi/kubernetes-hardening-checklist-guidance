# Appendix J: pod-reader RBAC role

To create a pod-reader role, create a YAML file with the following content:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
   namespace: your-namespace-name
name: pod-reader
rules:
- apiGroups: [""] # "" represents the core API group
   resources: ["pods"]
   verbs: ["get", "watch", "list"]
```

Application role:

```sh
kubectl apply --f role.yaml
```

To create a global pod-reader `ClusterRole`:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind:ClusterRole
metadata: default
# "namespace" is omitted because ClusterRoles is not bound to a namespace
   name: global-pod-reader
   rules:
   - apiGroups: [""] # "" represents the core API group
      resources: ["pods"]
      verbs: ["get", "watch", "list"]
```

Application role:

```sh
kubectl apply --f clusterrole.yaml
```