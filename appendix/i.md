#Appendix I: KMS configuration example

To encrypt a Secret with a Key Management Service (KMS) provider plug-in, you can use the following example encryption configuration YAML file to set properties for the provider. This example is based on [Kubernetes official documentation](https://kubernetes.io/docs/tasks/administer-cluster/kms-provider/).

```yaml
apiVersion: apiserver.config.k8s.io/v1
kind: EncryptionConfiguration
resources:
- resources:
   - secrets
   providers:
   - kms:
     name: myKMSPlugin
     endpoint: unix://tmp/socketfile.sock
     cachesize: 100
     timeout: 3s
   - identity: {}
```

To configure the API server to use a KMS provider, set the `--encryption-provider-config` flag along with the location of the configuration file and restart the API server.

To switch from a local encryption provider to KMS, add the KMS provider section in the `EncryptionConfiguration` file above the current encryption method as shown below.

```yaml
apiVersion: apiserver.config.k8s.io/v1
kind: EncryptionConfiguration
resources:
- resources:
   - secrets
     providers:
     - kms:
       name: myKMSPlugin
       endpoint: unix://tmp/socketfile.sock
       cachesize: 100
       timeout: 3s
     - aescbc:
       keys:
       - name: key1
         secret: <base64 encoded secret>
```

Restart the API server and run the following command to re-encrypt all secrets with the KMS provider.

```sh
kubectl get secrets --all-namespaces -o json | kubectl replace -f -
```