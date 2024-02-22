#Appendix C: Pod security policy example: 

PS: PSP are deprecated and removed since version v1.25

Below is an example of a Kubernetes Pod security policy that enforces strong security requirements for containers running in the cluster. This example is based on [official Kubernetes documentation](https://kubernetes.io/docs/concepts/policy/pod-security-policy/). Administrators are encouraged to modify this policy to meet their organization's requirements.

```yaml
apiVersion: policy/v1beta1
kind: PodSecurityPolicy
metadata:
   name: restricted
   annotations:
     seccomp.security.alpha.kubernetes.io/allowedProfileNames: 'docker/default,runtime/default'
     apparmor.security.beta.kubernetes.io/allowedProfileNames: 'runtime/default'
     seccomp.security.alpha.kubernetes.io/defaultProfileName: 'runtime/default'
     apparmor.security.beta.kubernetes.io/defaultProfileName: 'runtime/default'
spec:
   privileged: false # Need to prevent upgrade to root
     allowPrivilegeEscalation: false
     requiredDropCapabilities:
       - ALL
   volumes:
     -'configMap'
     - 'emptyDir'
     - 'projected'
     - 'secret'
     -'downwardAPI'
     - 'persistentVolumeClaim' # Assume persistentVolumes set by the administrator are safe
   hostNetwork: false
   hostIPC: false
   hostPID: false
   runAsUser:
     rule: 'MustRunAsNonRoot' #Require containers to run seLinux without root
     rule: 'RunAsAny' # Assume the node is using AppArmor instead of SELinux
     supplementalGroups:
       rule: 'MustRunAs'
       ranges: # Disable adding to root group
         - min: 1
           max: 65535
     runAsGroup:
       rule: 'MustRunAs'
       ranges: # Disable adding to root group
         - min: 1
           max: 65535
     fsGroup:
       rule: 'MustRunAs'
       ranges: # Disable adding to root group
         - min: 1
           max: 65535
   readOnlyRootFilesystem: true
```