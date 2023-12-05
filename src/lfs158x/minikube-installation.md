# Minikube -- installing local kubernetes clusters

## What is `minikube`?

- The easiest, most flexible and popular tool for setting up local kubernetes clusters
- Installs and runs on any native OS (Linux, Mac, Windows)
- For the best experience, install a [type-2 hypervisor](https://en.wikipedia.org/wiki/Hypervisor) or container runtime
- Minikube manages all cluster components and cleans up afterward leaving no trace of their existence
- Minikube can be run on bare metal or in an isolated VM with the help of a type-2 hypervisor
- Minikube will provision the nodes for the kubernetes cluster. This is ultimately limited by the hardware of the host system

### System requirements

1) VT-x/AMD-v virtualization must be enabled on the local workstation, and/or verify if it is supported.
2) The `kubectl` binary to access and manage kubernetes clusters. Can be installed with `minikube` or separately
3) Type-2 hypervisor or container runtime
  - On Linux:  VirtualBox, KVM2, and QEMU hypervisors, or Docker and Podman runtimes
  - On MacOS:  VirtualBox, HyperKit, VMware Fusion, Parallels, and QEMU hypervisors, or Docker runtime
  - On Windows: VirtualBox, Hyper-V, VMware Workstation, and QEMU hypervisors, or Docker runtime

To skip requirement 3, `minikube` supports running kubernetes components on bare metal with the `--driver=none` option (Advance use only).

`minikube` also requires an internet connection to download packages, dependencies, updates and pull images needed to initialize the Minikube Kubernetes cluster components.

### On Linux

> Instructions for the latest Minikube release on Ubuntu Linux 20.04 LTS with VirtualBox v7.0 specifically

Verify virtualization support -- (non-empty output means virtualization is supported)

```shell
$ grep -E --color 'vmx|svm' /proc/cpuinfo
```

Install VirtualBox

1) Add source repository

```shell
$ sudo bash -c 'echo "deb \
  [arch=amd64 signed-by=/usr/share/keyrings/oracle-virtualbox-2016.gpg] \
  https://download.virtualbox.org/virtualbox/debian \
  eoan contrib" >> /etc/apt/sources.list'
```

2) Download and register the public key

```shell
$ wget -O- \
  https://www.virtualbox.org/download/oracle_vbox_2016.asc | \
  sudo gpg --dearmor --yes \
  --output /usr/share/keyrings/oracle-virtualbox-2016.gpg
```

3) Update and install

```shell
$ sudo apt update
$ sudo apt install -y virtualbox-7.0
```

Install `minikube`

```shell
$ curl -LO \
  https://storage.googleapis.com/minikube/releases/latest/minikube_latest_amd64.deb
$ sudo dpkg -i minikube_latest_amd64.deb
```

Start `minikube`

```shell
$ minikube start
```

This should write to stdout what `minikube` is doing to bootstrap the cluster

> NOTE: An error message that reads "Unable to pick a default driver..." means that `minikube` was not able to locate any one of the supported hypervisors or runtimes. The recommendation is to install or re-install a desired isolation tool, and ensure its executable is found in the default PATH of your OS distribution. 

Check the cluster status

```
$ minikube status

minikube
type: Control Plane
host: Running
kubelet: Running
apiserver: Running
kubeconfig: Configured
```

Stop the cluster

```shell
$ minikube stop

✋  Stopping node "minikube"  ...
🛑  1 node stopped.
```

### On MacOS

> Instructions for the latest Minikube release on macOS with VirtualBox v7.0 specifically

Verify virtualization support -- (non-empty output means virtualization is supported)

```shell
$ sysctl -a | grep -E --color 'machdep.cpu.features|VMX'
```

Install VirtualBox by downloading and installing the `.dmg` package

Install `minikube`

```shell
$ curl -LO \
  https://storage.googleapis.com/minikube/releases/latest/minikube-darwin-amd64

$ sudo install minikube-darwin-amd64 /usr/local/bin/minikube
```

Start `minikube`

```shell
$ minikube start
```

This should write to stdout what `minikube` is doing to bootstrap the cluster

> NOTE: An error message that reads "Unable to pick a default driver..." means that `minikube` was not able to locate any one of the supported hypervisors or runtimes. The recommendation is to install or re-install a desired isolation tool, and ensure its executable is found in the default PATH of your OS distribution. 

Check the cluster status

```
$ minikube status

minikube
type: Control Plane
host: Running
kubelet: Running
apiserver: Running
kubeconfig: Configured
```

Stop the cluster

```shell
$ minikube stop

✋  Stopping node "minikube"  ...
🛑  1 node stopped.
```

### On Windows

> Instructions for the latest Minikube release on Windows 10 with VirtualBox v7.0 specifically

Verify virtualization support -- (multiple output lines ending with "yes" means yes)

```powershell
C:\WINDOWS\system32> systeminfo
```

Install VirtualBox by downloading and installing the appropriate `.exe` package

Install `minikube` by downloading and installing the latest [`minikube-installer.exe`](https://github.com/kubernetes/minikube/releases/latest/download/minikube-installer.exe) package

Start `minikube`

```shell
$ minikube start
```

This should write to stdout what `minikube` is doing to bootstrap the cluster

> NOTE: An error message that reads "Unable to pick a default driver..." means that `minikube` was not able to locate any one of the supported hypervisors or runtimes. The recommendation is to install or re-install a desired isolation tool, and ensure its executable is found in the default PATH of your OS distribution. 

Check the cluster status

```
$ minikube status

minikube
type: Control Plane
host: Running
kubelet: Running
apiserver: Running
kubeconfig: Configured
```

Stop the cluster

```shell
$ minikube stop

✋  Stopping node "minikube"  ...
🛑  1 node stopped.
```

## Advanced features

> For additional commands and usage options please visit the [Minikube command line reference](https://minikube.sigs.k8s.io/docs/commands/).

### The `minikube start` command

1) Selects a driver isolation software
2) Downloads the latest kubernetes version components
3) Provisions a single VM (called minkube) with the following hardware profile
  - CPUs=2
  - Memory=6GB
  - Disk=20GB
4) Bootstraps the control plane with `kubeadm` and installs the container runtime

This behavior can be modified to create custom clusters. Options are available to modify hardware profiles, isolation driver, cluster size and even which version of kubernetes to use. Some complex `start` commands are shown below

```shell
$ minikube start --kubernetes-version=v1.23.3 \
  --driver=podman --profile minipod

$ minikube start --nodes=2 --kubernetes-version=v1.24.4 \
  --driver=docker --profile doubledocker

$ minikube start --driver=virtualbox --nodes=3 --disk-size=10g \
  --cpus=2 --memory=4g --kubernetes-version=v1.25.1 --cni=calico \
  --container-runtime=cri-o -p multivbox

$ minikube start --driver=docker --cpus=6 --memory=8g \
  --kubernetes-version="1.24.4" -p largedock

$ minikube start --driver=virtualbox -n 3 --container-runtime=containerd \
  --cni=calico -p minibox
```

### The `minkube profile list` command

> The minikube profile command allows us to view the status of all our clusters in a table formatted output

```shell
$ minikube profile list

|----------|------------|---------|----------------|------|---------|---------|-------|--------|
| Profile  | VM Driver  | Runtime |       IP       | Port | Version | Status  | Nodes | Active |
|----------|------------|---------|----------------|------|---------|---------|-------|--------|
| minikube | virtualbox | docker  | 192.168.59.100 | 8443 | v1.25.3 | Running |     1 | *      |
|----------|------------|---------|----------------|------|---------|---------|-------|--------|

```

The active marker indicates the target cluster profile of the minikube command line tool. It can be changed like so 

```shell
$ minkube profile <name>
```

Individual profiles can be started and stopped with the `-p` switch on the respective commands

### The `minikube node` command

> A command that allows users to list the nodes of a cluster, add new control plane or worker nodes, delete existing cluster nodes, start or stop individual nodes of a cluster:

```shell
$ minikube node list

minikube 192.168.59.100

$ minikube node list -p minibox

minibox   192.168.59.101
minibox-m02   192.168.59.102
minibox-m03   192.168.59.103
```

### The `minikube ip` command

> To display the cluster control plane node's IP address, or another node's IP with the `--node` or `-n` flags:

```shell
$ minikube ip

192.168.59.100

$ minikube -p minibox ip

192.168.59.101

$ minikube -p minibox ip -n minibox-m02

192.168.59.102

```

### The `minikube delete` command

> When a cluster configuration is no longer of use, the cluster's profile can be deleted. It is also a profile aware command

```shell
$ minikube delete

🔥  Deleting "minikube" in virtualbox ...
💀  Removed all traces of the "minikube" cluster.

$ minikube delete -p minibox

🔥  Deleting "minibox" in virtualbox ...
🔥  Deleting "minibox-m02" in virtualbox ...
🔥  Deleting "minibox-m03" in virtualbox ...
💀  Removed all traces of the "minibox" cluster.
```
