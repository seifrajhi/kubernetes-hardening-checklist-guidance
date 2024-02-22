# Appendix H: Encryption Example

To encrypt secret data at rest, the encryption configuration file below provides an example to specify the required encryption type and encryption key. Storing the encryption key in an encrypted file only slightly improves security. The Secret will be encrypted, but the key will be accessed in the `EncryptionConfiguration` file. This example is based on [Kubernetes official documentation](https://kubernetes.io/docs/tasks/administer-cluster/encrypt-data/).

```yaml
apiVersion: apiserver.config.k8s.io/v1
kind: EncryptionConfiguration
resources:
- resources:
   - secrets
   providers:
   - aescbc:
     keys:
     - name: key1
       secret: <base 64 encoded secret>
   - identity: {}
```

To use this encryption file for encryption at rest, set the `--encryption-provider-config` flag when restarting the API server and indicate the location of the configuration file.