# Appendix G: ResourceQuota Example

Create a `ResourceQuota` object to limit overall resource usage within a namespace by applying a YAML file to the namespace or specifying requirements in the Pod's configuration file. The following example is an example of a namespace configuration file based on [Kubernetes official documentation](https://kubernetes.io/docs/tasks/administer-cluster/manage-resources/quota-memory-cpu-namespace/):

```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
   name: example-cpu-mem-resourcequota
spec:
   hard:
     requests.cpu: "1"
     requests.memory: 1Gi
     limits.cpu: "2"
     limits.memory: 2Gi
```

This `ResourceQuota` can be applied like this:

```sh
kubectl apply -f example-cpu-mem-resourcequota.yaml -- namespace=<insert-namespace-here>
```

This `ResourceQuota` imposes the following restrictions on the selected namespace:

- Each container must have a memory request, memory limit, CPU request, and CPU limit.
- Total memory requests across all containers should not exceed 1 GiB
- The total memory limit for all containers should not exceed 2 GiB
- Total CPU requests for all containers should not exceed 1 CPU
- The total CPU limit for all containers should not exceed 2 CPUs