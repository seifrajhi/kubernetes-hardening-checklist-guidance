# Network isolation and reinforcement

Cluster networking is a core concept of Kubernetes. Communication between containers, Pods, services and external services must be taken into account. By default, there are few network policies to isolate resources and prevent lateral movement or upgrades if the cluster is compromised. Resource isolation and encryption are effective ways to limit the movement and escalation of network actors within a cluster.

**key point**

- Use network policies and firewalls to isolate resources.
- Securing the control plane.
- Encrypt traffic and sensitive data (such as Secrets) at rest.

## Namespaces

Kubernetes namespaces are a way to divide cluster resources among multiple individuals, teams, or applications within the same cluster. **By default, namespaces are not automatically isolated**. However, the namespace does assign a label to a scope, which can be used to specify authorization rules through RBAC and network policies. In addition to network isolation, policies can limit storage and computing resources to provide greater control over Pods at the namespace level.

There are three namespaces by default, which cannot be deleted:

- `kube-system` (for Kubernetes components)
- `kube-public` (for public resources)
- `default` (for user resources)

User Pods should not be placed in `kube-system` or `kube-public` as these are reserved for cluster services. YAML files, such as **[Appendix D: Namespace Example](appendix/d.md)**, can be used to create new namespaces. Pods and services in different namespaces can still communicate with each other unless additional isolation measures are in place, such as network policies.

## Network Policy

Network policies control traffic between pods, namespaces, and external IP addresses. By default, no network policies are applied to Pods or namespaces, resulting in unrestricted ingress and egress traffic within the Pod network. Pods are quarantined through the network policy that applies to the Pod or Pod namespace. Once a Pod is selected in a network policy, it will deny any connections that are not allowed by any applicable policy object.

To create a network policy, you need a network plugin that supports the `NetworkPolicy` API. Use the `podSelector` and/or `namespaceSelector` options to select Pods. An example network policy is shown in [**Appendix E**](appendix/e.md). The format of the network policy may vary depending on the Container Network Interface (CNI) plugin used by the cluster. Administrators should use the default policy of selecting all Pods to deny all ingress and egress traffic and ensure that any unselected Pods are quarantined. Additional policies can then relax these restrictions on allowed connections.

External IP addresses can be used in ingress and egress policies using `ipBlock`, but different CNI plugins, cloud providers or service implementations may affect the order in which `NetworkPolicy` is processed and the rewriting of addresses within the cluster.

## Resource Policy

In addition to network policies, `LimitRange` and `ResourceQuota` are two policies that can limit the resource usage of a namespace or node. The `LimitRange` policy limits individual resources per Pod or container within a specific namespace, for example, by enforcing maximum compute and storage resources. Only one `LimitRange` constraint can be created per namespace, as shown in the `LimitRange` example in **[Appendix F](appendix/f.md)**. Kubernetes 1.10 and newer support `LimitRange` by default.

Unlike the `LimitRange` policy, `ResourceQuotas` is a limit on the total resource usage of the entire namespace, such as a limit on the total amount of CPU and memory usage. If the user attempts to create a Pod that violates the `LimitRange` or `ResourceQuota` policy, the Pod creation fails. An example of a `ResourceQuota` strategy is shown in [**Appendix G**](appendix/g.md).

## Control plane reinforcement

The control plane is the core of Kubernetes, enabling users to view containers, schedule new Pods, read Secrets, and execute commands in the cluster. Because of these sensitive functions, control planes should be highly protected. In addition to security configurations such as TLS encryption, RBAC, and strong authentication methods, network isolation can help prevent unauthorized users from accessing the control plane. The Kubernetes API server runs on ports 6443 and 8080, which should be protected by a firewall and only accept intended traffic. Port 8080, by default, is accessible from the local machine without TLS encryption, requesting bypassing the authentication and authorization modules. Insecure ports can be disabled using the API server flag `--insecure-port=0`. Kubernetes API servers should not be exposed to the internet or untrusted networks. Network policies can be applied to the `kube-system` namespace to restrict internet access to `kube-system`. If the default deny policy is enforced for all namespaces, the kube-system namespace must still be able to communicate with other control plane and worker nodes.

The following table lists the control plane ports and services.

**Table 2: Control Plane Ports**

| port | direction | port range | destination                        |
| ---- | ------- | ---------------------------- | -------------------------------- |
| TCP  | Inbound | 6443 or 8080 if not disabled | Kubernetes API server            |
| TCP  | Inbound | 2379-2380                    | etcd server client API           |
| TCP  | Inbound | 10250                        | kubelet API                      |
| TCP  | Inbound | 10251                        | kube-scheduler                   |
| TCP  | Inbound | 10252                        | kube-controller-manager          |
| TCP  | Inbound | 10258                        | cloud-controller-manager（可选） |

### Etcd

> The etcd backend database is a critical control plane component and the most important security part of the cluster.

The etcd backend database stores state information and cluster secrets. It is a critical control plane component, and gaining write access to etcd can allow a network actor to gain root access to the entire cluster. Etcd can only be accessed through the API server, and users can be restricted by the cluster's authentication method and RBAC policy. The etcd data store can run on a separate control plane node, allowing firewalls to restrict access to the API server. Administrators should set up TLS certificates to enforce HTTPS communication between etcd server and API server. The etcd server should be configured to only trust the certificate assigned to the API server.

### Kubeconfig file

`kubeconfig` files contain sensitive information about the cluster, users, namespaces and authentication mechanisms. Kubectl uses configuration files stored in the `$HOME/.kube` directory of worker nodes and controls the plane local machine. A network actor could exploit access to this configuration directory to obtain and modify configurations or credentials, further compromising the cluster. Configuration files should be protected against unintentional changes, and unauthenticated non-root users should be blocked from accessing these files.

## Work node division

The worker node can be a virtual machine or a physical machine, depending on the cluster implementation. Because nodes run microservices and host the cluster's network applications, they are often targets of attacks. If a node is compromised, administrators should proactively limit the attack surface by separating worker nodes from other network segments that do not need to communicate with worker nodes or Kubernetes services. Firewalls can be used to separate internal network segments from external-facing worker nodes or the entire Kubernetes service, depending on the network. Confidential databases or internal services that do not require internet access, which may need to be separated from the possible attack surface of worker nodes.

The following table lists the worker node ports and services.

**Table 3: Worker node ports**

| port | direction | port range | destination |
| ---- | ------- | ----------- | ----------------- |
| TCP  | Inbound | 10250       | kubelet API       |
| TCP  | Inbound | 30000-32767 | NodePort Services |

## Encryption

Administrators should configure all traffic in a Kubernetes cluster—including traffic between components, nodes, and control plans—to be encrypted using TLS 1.2 or 1.3.

Encryption can be set during installation or created after installation using TLS boot (see [Kubernetes documentation](https://kubernetes.io/docs/tasks/administer-cluster/securing-a-cluster/)) and distribute certificates to nodes. As with all methods, certificates must be distributed between nodes in order to communicate securely.

## Secret

> By default, Secrets are stored as unencrypted base64-encoded strings and can be retrieved by anyone with API permissions.

Kubernetes Secret maintains sensitive information such as passwords, OAuth tokens, and SSH keys. Storing sensitive information in Secrets provides greater access control than storing passwords or tokens in YAML files, container images, or environment variables. By default, Kubernetes stores Secrets as unencrypted base64-encoded strings that can be retrieved by anyone with API permissions. Access can be restricted by applying an RBAC policy to the **secret** resource.

Secrets can be encrypted by configuring data-at-rest encryption on the API server or by using an external Key Management Service (KMS), which can be provided through a cloud provider. To enable encryption of Secret data at rest using the API server, administrators should modify the `kube-apiserver` manifest file to execute with the `--encryption-provider-config` parameter. An example of an `encryption-provider-config` is shown in [Appendix H](appendix/h.md): Encryption Example. Using a KMS provider prevents the original encryption keys from being stored on local disk. To encrypt a Secret with a KMS provider, the KMS provider should be specified in the `encryption-provider-config` file, as shown in the KMS configuration example in [Appendix I](appendix/i.md).

After applying the `encryption-provider-config` file, administrators should run the following commands to read and encrypt all Secrets.

```sh
kubectl get secrets --all-namespaces -o json | kubectl replace -f -
```

## Protect sensitive cloud infrastructure

Kubernetes is typically deployed on virtual machines in cloud environments. Therefore, administrators should carefully consider the attack surface of the virtual machines running Kubernetes worker nodes. In many cases, pods running on these VMs can access sensitive cloud metadata services on non-routable addresses. These metadata services provide cyber actors with information about cloud infrastructure and perhaps even short-lived credentials for cloud resources. Cyber actors abuse these metadata services for privilege escalation. Kubernetes administrators should prevent pods from accessing cloud metadata services by using network policies or through cloud configuration policies. Because these services vary depending on the cloud provider, administrators should follow the vendor's guidance to harden these access vectors.