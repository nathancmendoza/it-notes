# Kubernetes

## What are kubernetes?

> "Kubernetes is an open-source system for automating deployment, scaling, and management of containerized applications".

- Derived from the Greek work "κυβερνήτης", which translates directly to *helmsman* or *ship pilot*
- This makes kubernetes analogous to the pilot on a ship of containers
- Kubernetes is also known as "k8s' (pronounced kate's), as there are 8 characters between `k` and `s`
- Highly inspired by Google's own workload manager Borg
- Project's initial release was July 2015 and is part of the cloud native computing foundation

### Why use kubernetes?

- **Portability**: can be deployed on local or remote VMs, bare metal, or in many different cloud setups
- **Extensibility**: 
  - Supports and is supported by many 3rd party tools
  - Orchestrate microservice type applications by itself being a microservice application
  - Support for writing custom resources, operators, APIs, and scheduling rules or plugins
**Community support**: A successful open-source project is of 3,000 contributors and 100,000 commits

### Features of Kubernetes

- **Automatic bin packing**: Kubernetes automatically schedules containers based on resource needs and constraints, to maximize utilization without sacrificing availability.
- **Designed for extensibility**: A Kubernetes cluster can be extended with new custom features without modifying the upstream source code.
- **Self-healing**: Kubernetes automatically replaces and reschedules containers from failed nodes. It terminates and then restarts containers that become unresponsive to health checks, based on existing rules/policy. It also prevents traffic from being routed to unresponsive containers.
- **Horizontal scaling**: With Kubernetes applications are scaled manually or automatically based on CPU or custom metrics utilization.
- **Service discovery and load balancing**: Containers receive IP addresses from Kubernetes, while it assigns a single Domain Name System (DNS) name to a set of containers to aid in load-balancing requests across the containers of the set.
- **Automated rollouts and rollbacks**: Kubernetes seamlessly rolls out and rolls back application updates and configuration changes, constantly monitoring the application's health to prevent any downtime.
- **Secrets and configuration management**: Kubernetes manages sensitive data and configuration details for an application separately from the container image, in order to avoid a rebuild of the respective image. Secrets consist of sensitive/confidential information passed to the application without revealing the sensitive content to the stack configuration, like on GitHub.
- **Storage orchestration**: Kubernetes automatically mounts software-defined storage (SDS) solutions to containers from local storage, external cloud providers, distributed storage, or network storage systems.
- **Batch execution**: Kubernetes supports batch execution, long-running jobs, and replaces failed containers.
- **IPv4 and IPv6 dual stack**: Kubernetes supports both IPv4 and IPv6 addresses.

## Evolution of kubernetes

### From Borg to Kubernetes

- Borg is Google's secret for running is worldwide containerized applications in production
- All services Google provides (Gmail, Drive, Maps, etc.) are serviced by Borg
- Initial developers of kubernetes were Google employees who worked on Borg

### Cloud native computing foundation

- One of the largest subprojects hosted by the [Linux Foundation](https://www.linuxfoundation.org/)
- Aims to accelerate adoption of containers, microservices, and cloud-native applications
- Hosts a multitude of projects, each operating independently under its pre-governance structure and existing maintainers
- Projects are categorized by their maturity levels: Sandbox, Incubating, Graduated
- Kubernetes is, of course, one of over a dozen projects that have graduated, with many more incubating or in the sandbox
- Not all project take-off, and as activity fizzles or demand disappears, projects can be archived

### CNCF and kubernetes

- Provides a neutral home for the Kubernetes trademark and enforces proper usage.
- Provides license scanning of core and vendor code.
- Offers legal guidance on patent and copyright issues.
- Creates and maintains open source learning curriculum, training, and certification for Kubernetes and cloud native associates (KCNA), administrators (CKA), application developers (CKAD), and security specialists (CKS).
- Manages a software conformance working group.
- Actively markets Kubernetes.
- Supports ad hoc activities.
- Sponsors conferences and meetup events.
