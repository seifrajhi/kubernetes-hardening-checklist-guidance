# Appendix E: Network Policy Example

Network policies vary depending on the network plug-in used. The following is an example of a network policy. Refer to [Kubernetes documentation](https://kubernetes.io/docs/tasks/administer-cluster/declare-network-policy/) to limit access to the nginx service to Pods with labeled access. superior.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
   name: example-access-nginx
   namespace: prod #This can be any namespace, or omitted if no namespace is used.
spec:
   podSelector:
     matchLabels:
       app: nginx
   ingress:
     - from:
       - podSelector:
         matchLabels:
           access: "true"
```

The new `NetworkPolicy` can be applied in the following ways:

```sh
kubectl apply -f policy.yaml
```

A default policy that denies all entries:

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
   name: deny-all-ingress
spec:
   podSelector: {}
   policyType:
     -Ingress
```

  A default policy that denies all exports:

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
   name: deny-all-egress
spec:
   podSelector: {}
   policyType:
   - Egress
```