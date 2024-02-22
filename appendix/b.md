# Appendix B: Deployment template example for read-only file system

Below is an example of a Kubernetes deployment template using a read-only root file system.

```yaml
apiVersion: apps/v1
Kind: Deployment
metadata:
   labels:
     app: web
     name: web
   spec:
     selector:
       matchLabels:
         app: web
     template:
       metadata:
         labels:
           app: web
           name: web
       spec:
         containers:
         - command: ["sleep"]
           args: ["999"]
           image:ubuntu:latest
           name:web
           securityContext:
             readOnlyRootFilesystem: true #Make the container's file system read-only
           volumeMounts:
             - mountPath: /writeable/location/here #Create a writable volume
               name:volName
         volumes:
         - emptyDir: {}
           name:volName
```