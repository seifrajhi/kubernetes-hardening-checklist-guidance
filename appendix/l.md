# Appendix L: Audit Strategy

Here is an audit policy that logs all audit events at the highest level:

```yaml
apiVersion: audit.k8s.io/v1
Kind: Policy
rules:
   - level: RequestResponse
# This audit policy records all audit events at the RequestResponse level
```

This audit strategy logs all events at the highest level. If an organization has the resources available to store, parse, and examine large volumes of logs, logging all events at the highest level is a good way to ensure that when an event occurs, all necessary contextual information is present in the logs. If resource consumption and availability are an issue, then additional logging rules can be established to reduce logging levels for non-critical components and general unprivileged operations, as long as the auditing requirements of the system are met. Examples of how to set up these rules can be found in the [Kubernetes official documentation](https://kubernetes.io/docs/tasks/debug-application-cluster/audit/).