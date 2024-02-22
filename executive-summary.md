# executive Summary

Kubernetes® is an open source system that automates the deployment, scaling, and management of applications running in containers and often hosted in cloud environments. Using this type of virtualized infrastructure can provide some flexibility and security benefits compared to traditional monolithic software platforms. However, securely managing everything from microservices to the underlying infrastructure introduces additional complexities. The hardening guidance detailed in this report is designed to help organizations deal with the associated risks and reap the benefits of using this technology.

Three common sources of breaches in Kubernetes are supply chain risks, malicious actors, and insider threats.

Supply chain risks are often challenging and can arise during the container build cycle or infrastructure acquisition. Malicious threat actors can exploit vulnerabilities and misconfigurations in components of the Kubernetes architecture, such as the control plane, worker nodes, or containerized applications. Insider threats can be administrators, users, or cloud service providers. Insiders with special access to an organization's Kubernetes infrastructure may abuse these privileges.

This guide describes the security challenges associated with setting up and securing a Kubernetes cluster. Includes hardening strategies to avoid common misconfigurations and guides system administrators and developers of national security systems on how to deploy Kubernetes, with configuration examples of recommended hardening and mitigation measures. This guide details the following mitigation measures:

- Scan containers and pods for vulnerabilities or misconfigurations.
- Run containers and pods with as few permissions as possible.
- Use network isolation to control the amount of damage a vulnerability can cause.
- Use firewalls to restrict unwanted network connections and encryption to protect confidentiality.
- Use strong authentication and authorization to restrict user and administrator access and limit the attack surface.

Use log auditing so administrators can monitor activity and warn of potentially malicious activity.

Regularly review all Kubernetes setups and use vulnerability scanning to help ensure risks are properly considered and security patches applied.

For additional security hardening guidance, see the Center for Internet Security Kubernetes Benchmarks, Docker and Kubernetes Security Technology Implementation Guide, Cybersecurity and Infrastructure Security Agency (CISA) Analytical Reports, and Kubernetes documentation.