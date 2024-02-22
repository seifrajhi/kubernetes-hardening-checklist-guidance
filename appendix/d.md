#Appendix D: Namespace Example

The following example creates a Kubernetes namespace for each team or user group using the kubectl command or YAML file. Any name prefixed with `kube` should be avoided as it may conflict with the namespace reserved by the Kubernetes system.

Kubectl command to create a namespace.

```sh
kubectl create namespace <insert-namespace-name-here>
```

To create a namespace using a YAML file, create a new file named my-namespace.yaml with the following content:

```yaml
apiVersion: v1
kind: Namespace
metadata:
   name: <insert-namespace-name-here>
```

To apply the namespace, use:

```sh
kubectl create –f ./my-namespace.yaml
```

To create a new Pod in an existing namespace, switch to the desired namespace:

```sh
kubectl config use-context <insert-namespace-here>
```

To apply a new Deployment, use:

```sh
kubectl apply -f deployment.yaml
```

Alternatively, you can add the namespace to the kubectl command using:

```sh
kubectl apply -f deployment.yaml --namespace=<insert-namespace-here>
```

Or specify `namespace:<insert-namespace-here>` under metadata in the YAML declaration.

Once created, resources cannot be moved between namespaces. The resource must be deleted and created in a new namespace.