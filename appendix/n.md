#Appendix N: webhook configuration

YAML file example:

```yaml
apiVersion: v1
Kind: Config
preferences: {}
clusters:
   - name: example-cluster
     cluster:
       server: http://127.0.0.1:8080
       #web endpoint address for the log files to be sent to
       name: audit-webhook-service
     users:
   - name: example-users
     user:
       username: example-user
       password: example-password
   contexts:
   - name: example-context
     context:
       cluster: example-cluster
       user: example-user
    current-context: example-context
#source: https://dev.bitolog.com/implement-audits-webhook/
```

Audit events sent by webhooks are sent as HTTP POST requests with JSON audit events included in the request body. The specified address should point to an endpoint capable of accepting and parsing these audit events, whether it is a third-party service or an in-house configured endpoint.

Example flags for submitting a webhook configuration file to `kube-apiserve`r:

Edit the `kube-apiserver.yaml` file in the control plane

```sh
sudo vi /etc/kubernetes/manifests/kube-apiserver.yaml
```

Add the following text in the kube-apiserver.yaml file

```
--audit-webhook-config-file=/etc/kubernetes/policies/webhook-policy.yaml
--audit-webhook-initial-backoff=5
--audit-webhook-mode=batch
--audit-webhook-batch-buffer-size=5
```

The `audit-webhook-initial-backoff` flag determines how long to wait after an initial failed request before retrying. The available webhook modes are `batch`, `block` and `blocking-stric`. When using batch mode, it is possible to configure maximum wait time, buffer size, etc. The official Kubernetes documentation contains more details on other configuration options [audit](https://kubernetes.io/docs/tasks/debug-application-cluster/audit/) and [`kube-apiserver`](https:// kubernetes.io/docs/reference/command-line-tools-reference/kube-apiserver/).