# Appendix F: LimitRange Example

In Kubernetes 1.10 and newer, `LimitRange` support is enabled by default. The following YAML file specifies a `LimitRange` for each container, which has a default request and limit, as well as a minimum and maximum request.

```yaml
apiVersion: v1
kind: LimitRange
metadata:
   name: cpu-min-max-demo-lr
spec:
   limits
   -default:
       cpu: 1
     defaultRequest:
       CPU: 0.5
     max:
       cpu: 2
     min:
       CPU 0.5
     type: Container
```

`LimitRange` can be applied to namespaces, using:

```sh
kubectl apply -f <example-LimitRange>.yaml --namespace=<Enter-Namespace>
```

After applying this `LimitRange` configuration example, all containers created in the namespace will be assigned the default CPU requests and limits if not specified. The CPU requests of all containers in the namespace must be greater than or equal to the minimum value and less than or equal to the maximum CPU value, otherwise the container will not be instantiated.