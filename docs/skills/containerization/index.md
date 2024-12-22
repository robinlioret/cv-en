# Containerization

Containerization involves many moving parts from the build tools to the orchestration, passing through security
analysis, runtime and many more.

## Build tools

[=90% "Docker"]
[=10% "Buildpacks (just started learning)"]

## Runtime

[=90% "Docker"]
[=30% "Containerd (still learning)"]

## Kubernetes' Core

[=95% "Application deployment"]
[=90% "Networking"]
[=85% "Permission management"]
[=70% "Storage management"]
[=70% "Cluster build and management"]

## Kubernetes' Ecosystem

[=90% "Helm"]
[=90% "KEDA"]
[=85% "ArgoCD"]
[=80% "Rancher"]
[=80% "Headlamp"]
[=75% "Nginx Ingress Controller"]
[=75% "AWS PodIdentity"]
[=75% "AWS Secret Store CSI Driver"]
[=60% "Crossplane"]
[=60% "Tekton"]
[=30% "Kyverno (still learning)"]
[=30% "Cilium (still learning)"]
[=30% "Vault (still learning)"]
[=20% "Open Telemetry (still learning)"]

[//]: # (@formatter:off)
/// admonition | Kubernetes' technologies I'll explore as soon as possible
    type: tip

* CloudnativePG: a Kubernetes hosted PostgreSQL database operator
* MariaDB Operator: a Kubernetes hosted MariaDB database operator
* Istio: service meshing and more. I'm waiting for the Gateway API project to mature
* Falco: security service for live detection of intrusions
* Kubevirt: virtualization tool, could be useful to create scalable and secure VDIs
* Harbor: OSS container and artifact registry
* Keyclock: Identity provider

And many more, Kubernetes' ecosystem is so rich and empowering ! There are many ways to consolidate 
the [pillars](../../gists/pillars.md).

///
[//]: # (@formatter:on)


