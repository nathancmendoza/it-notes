# Installing kubernetes

## Configuration options

Kubernetes can be installed using different clustering configurations. Most common types are listed below

- **All-in-One Single-Node Installation**: In this setup, all the control plane and worker components are installed and running on a single-node. While it is useful for learning, development, and testing, it is not recommended for production purposes.
- **Single-Control Plane and Multi-Worker Installation**: In this setup, we have a single-control plane node running a stacked [`etcd`](./kubernetes-architecture.md#key-value-store) instance. Multiple worker nodes can be managed by the control plane node.
- **Single-Control Plane with Single-Node `etcd`, and Multi-Worker Installation**: In this setup, we have a single-control plane node with an external [`etcd`](./kubernetes-architecture.md#key-value-store) instance. Multiple worker nodes can be managed by the control plane node.
- **Multi-Control Plane and Multi-Worker Installation**: In this setup, we have multiple control plane nodes configured for High-Availability (HA), with each control plane node running a stacked [`etcd`](./kubernetes-architecture.md#key-value-store) instance. The `etcd`` instances are also configured in an HA `etcd` cluster and multiple worker nodes can be managed by the HA control plane.
- **Multi-Control Plane with Multi-Node [`etcd`](./kubernetes-architecture.md#key-value-store), and Multi-Worker Installation**: In this setup, we have multiple control plane nodes configured in HA mode, with each control plane node paired with an external `etcd` instance. The external `etcd` instances are also configured in an HA `etcd` cluster, and multiple worker nodes can be managed by the HA control plane. This is the most advanced cluster configuration recommended for production environments. 

## Infrastructure considerations

### Local clusters

- There are a variety of tools that can be used to install kubernetes clusters locally.
- The one extensively used in this course in [Minikube](https://minikube.sigs.k8s.io/docs/)

### Production clusters

There are tools that can bootstrap kubernetes clusters, some that can provision necessary hosts on underlying hardware

- [`kubeadm`](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/): A first-class citizen of the Kubernetes ecosystem. It is a secure and recommended method to bootstrap a multi-node production ready Highly Available Kubernetes cluster, on-premises or in the cloud. `kubeadm` can also bootstrap a single-node cluster for learning. It has a set of building blocks to set up the cluster, but it is easily extendable to add more features. Please note that `kubeadm` does not support the provisioning of hosts - they should be provisioned separately with a tool of our choice.
- [`kubespray`](https://kubernetes.io/docs/setup/production-environment/tools/kubespray/): Allows installation of Highly Available production ready Kubernetes clusters on AWS, GCP, Azure, OpenStack, vSphere, or bare metal. `kubespray` is based on Ansible, and is available on most Linux distributions.
- [`kops`](https://kubernetes.io/docs/setup/production-environment/tools/kops/): Enables us to create, upgrade, and maintain production-grade, Highly Available Kubernetes clusters from the command line. It can provision the required infrastructure as well. Currently, AWS is officially supported. Support for DigitalOcean and OpenStack is in beta, Azure and GCE is in alpha support, and other platforms are planned for the future.
- [Kubernetes the hard way](https://github.com/kelseyhightower/kubernetes-the-hard-way): An extremely helpful installation guide and resource for a manual installation approach. The project aims to teach all the detailed steps involved in the bootstrapping of a Kubernetes cluster, steps that are otherwise automated by various tools mentioned in this chapter and obscured from the end user.

### Certified solutions providers

The growing popularity of Kubernetes made demand for hosted platforms of certified kubernetes distributions.

- **Hosted solutions**: These providers fully manage the provided software stack. Users pay for hosting and management charges
- **Partners**: Providers offering managed kubernetes services and platforms
- **Turnkey cloud solutions**: Offering production ready kubernetes clusters on cloud architecture

### Kubernetes on windows

- Windows plays a key role in running and managing enterprise applications and services
- Version 1.14 brought windows support to *only* worker nodes of kubernetes clusters
  - Supports deployment of windows containers in a kubernetes clusters
  - Control plane nodes are limited to running on Linux and no plans are in place to extend windows support to control planes
- When working with Linux/Windows hybrid clusters, the user is responsible to configure the workload scheduling according to the expected OS
