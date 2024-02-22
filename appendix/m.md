# Appendix M: Example of flags for submitting audit policy files to kube-apiserver

In the control plane, open the `kube-apiserver.yaml` file with a text editor. Editing the `kube-apiserver` configuration requires administrator privileges.

```sh
sudo vi /etc/kubernetes/manifests/kube-apiserver.yaml
```

Add the following text to the `kube-apiserver.yaml` file:

```
--audit-policy-file=/etc/kubernetes/policy/audit-policy.yaml --audit-log-path=/var/log/audit.log --audit-log-maxage=1825
```

The `audit-policy-file` flag should be set to the path to the audit policy, and the `audit-log-path` flag should be set to a safe location where the required audit logs are written. There are some other flags, such as the `audit-log-maxage` flag shown here, which specifies the maximum number of days that logs should be kept, and there are also flags that specify the maximum number of audit log files to retain, the maximum Log file size in megabytes, etc. The only necessary flags to enable logging are the `audit-policy-file` and `audit-log-path` flags. Other flags can be used to configure logging to comply with your organization's policies.

If the user's `kube-apiserver` is running as a Pod, then it is necessary to mount the volume and configure the `hostPath` of the policy and log file location to preserve audit records. This can be done by adding the following section to the `kube-apiserver.yaml` file as pointed out [in the Kubernetes documentation](https://kubernetes.io/docs/tasks/debug-application-cluster/audit/):

```yaml
   volumeMounts:
     - mountPath: /etc/kubernetes/audit-policy.yaml
       name: audit
       readOnly: true
     - mountPath: /var/log/audit.log
       name: audit-log
       readOnly: false
volumes:
- hostPath:
     path: /etc/kubernetes/audit-policy.yaml
     type: File
   name: audit
- hostPath:
     path: /var/log/audit.log
     type: FileOrCreate
   name: audit-log
```