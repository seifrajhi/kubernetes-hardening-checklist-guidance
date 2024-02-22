# Appendix K: RBAC RoleBinding and ClusterRoleBinding examples

To create a `RoleBinding`, create a YAML file with the following content:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
# This role binding allows "jane" to read the Pod namespace of "your-namespace-name"
# You need to already have a role named "pod-reader" in this namespace.
kind: RoleBinding
metadata:
   name: read-pods
   namespace: your-namespace-name
   subjects: # You can specify more than one "subject"
- kind: User
   name: jane # "name" is case sensitive
   apiGroup: rbac.authorization.k8s.io
   roleRef: # "roleRef" specifies binding to a Role/ClusterRole
      kind: Role # Must be Role or ClusterRole
      name: pod-reader # This must match the name of the Role or ClusterRole you want to bind
      apiGroup: rbac.authorization.k8s.io
```

Apply `RoleBinding`:

```sh
kubectl apply --f rolebinding.yaml
```

To create a `ClusterRoleBinding`, create a YAML file with the following content:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
# This cluster role binding allows anyone in the "manager" group to read Pod information in any namespace.
kind: ClusterRoleBinding
metadata:
   name: global-pod-reader
subjects: # You can specify more than one "subject"
   - kind: Group
     name: manager # Name is case sensitive
     apiGroup: rbac.authorization.k8s.io
     roleRef: # "roleRef" specifies binding to a Role/ClusterRole
       kind: ClusterRole # Must be Role or ClusterRole
       name: global-pod-reader # This must match the name of the Role or ClusterRole you want to bind
       apiGroup: rbac.authorization.k8s.io
```

Apply `RoleBinding`:

```sh
kubectl apply --f clusterrolebinding.yaml
```