# Advanced kubernetes topics

## Annotations

- Allows attachment of arbitrary, non-identifying to any object in a key-value format
- **Not** used to identify and select objects, but used to store information like
  - Build/release IDs, PR numbers, git branch
  - Phone/pager number of people responsible
  - Pointers to logging, monitoring, analytics, audit repositories, and debugging tools
  - Ingress controller information
  - Deployment state and revision information
- Annotations are displayed while describing an object

```shell
$ kubectl describe deployment webserver

Name:                webserver
Namespace:           default
CreationTimestamp:   Fri, 25 Mar 2022 05:10:38 +0530
Labels:              app=webserver
Annotations:         deployment.kubernetes.io/revision=1
                     description=Deployment based PoC dates 2nd Mar'2022
... 
```

## Quota and limits management

When sharing a kubernetes cluster, fair usage is a constant concern and preventing a single user from taking an undue advantage is important. Administrators can utilize the [`ResourceQuota`](https://kubernetes.io/docs/concepts/policy/resource-quotas/) API to limit aggregate resource consumption per namespace. The following quota types can be set on a namespace basis:

- **Compute resource quota**: limits the total sum of compute resources (CPU, memory) that can be requested
- **Storage resource quota**: limits the total sum of storage resources that can be requested
- **Object count quota**: restricts the number of objects of a given type

Additionally, a [`LimitRange`](https://kubernetes.io/docs/concepts/policy/limit-range/) can help limit resource allocation to pods and containers in a namespace. When used in conjunction with resource quotas, limit ranges can:

- Set compute resources usage limits per Pod or Container in a namespace.
- Set storage request limits per `PersistentVolumeClaim` in a namespace.
- Set a request to limit ratio for a resource in a namespace.
- Set default requests and limits and automatically inject them into Containers' environments at runtime.

## Auto-scaling

Scaling a few kubernetes objects might be fairly easy, but is not practical for a production ready cluster where hundreds or even thousands of objects are deployed. Auto-scaling is a dynamic scaling solution that adjusts the number of objects from the cluster based on resource utilization, availability, and requirements

- [Horizontal Pod Autoscaler (HPA)](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/): An algorithm-based controller which automatically adjusts the number of replicas in a `ReplicaSet`, `Deployment` or `Replication` Controller based on CPU utilization.
- [Vertical Pod Autoscaler (VPA)](https://github.com/kubernetes/autoscaler/tree/master/vertical-pod-autoscaler#readme): Automatically sets Container resource requirements (CPU and memory) in a Pod and dynamically adjusts them in runtime, based on historical utilization data, current resource availability and real-time events.
- [Cluster Autoscaler](https://github.com/kubernetes/autoscaler/tree/master/cluster-autoscaler#cluster-autoscaler): Automatically [resizes the kubernetes cluster](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md) when there are insufficient resources available for new pods or when there are underutilized nodes in the cluster

## Jobs and cron jobs

[Jobs](https://kubernetes.io/docs/concepts/workloads/controllers/jobs-run-to-completion/) create one or more pods to complete a given task. Jobs handle pod failure automatically and ensures the given tasks is successfully completed. Jobs can be configured for:

- **Parallelism**: sets the number of pods allowed to run in parallel
- **Completions**: sets the number of expected completions
- **Active deadline seconds**: sets the duration of the job
- **Backoff limit**: sets the number of retries before the job is marked as failed
- **TTL after finished**: sets the delay to cleanup finished jobs

Jobs that need to be performed at scheduled times/dates can be set up as [Cron Jobs](https://kubernetes.io/docs/tasks/job/automated-tasks-with-cron-jobs/). A cron job creates a new job object once per execution cycle. They can be configured with

- **Starting deadline seconds**: sets the deadline to start of job if the scheduled time was missed
- **Concurrency policy**: allows or forbids concurrent jobs or to replace old jobs with new ones

## Stateful sets

A [`StatefulSet`](https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/) controller is used for applications that require a unique identity, such as a name, network identifications, or strict ordering. The controller provides identity and guaranteed ordering of deployment and scaling to pods. However, the `StatefulSet` controller has very strict Service and Storage Volume dependencies that make it challenging to configure and deploy.

## Custom resources

> In Kubernetes, a **resource** is an API endpoint which stores a collection of API objects. For example, a Pod resource contains all the Pod objects.

For most cases, existing kubernetes resources are sufficient to fulfill requirements. But kubernetes allows creation of **custom resources** without modifying the source code. Custom resources are dynamic in nature, they can appear or disappear in an already running cluster. To add custom resources:

- [Custom resource definitions](https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/): The easy way to add a custom resource. Does not require programming knowledge, but a custom controller would.
- [API aggregation](https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/apiserver-aggregation/): subordinate API services which sit behind the API server. The primary API server acts as a proxy for all incoming requests. It handles the ones based on its capabilities and proxies over other requests meant for subordinate API services.

## Kubernetes federation

- Manage multiple kubernetes clusters from a single control plane
- Sync resources across federated clusters and enable cross-cluster discovery

## Security context and pod security admission

- [Security Context](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/)
  - Define specific privileges and access control settings for Pods and Containers
  - Set Discretionary Access Control for object access permissions, privileged running, capabilities, security labels,
  - Limited to the individual Pods and Containers where such context configuration settings are incorporated in the `spec` section
- [Pod Security Admission](https://kubernetes.io/docs/concepts/security/pod-security-admission/)
  - Enforce pod security standards at the namespace level
  - Security levels are defined by sets of pod security context controls that are pre-determined for each standard

## Network policies

- Initial design would allow all pods to communicate freely, but became clear that was not an ideal design
- [Network policies](https://kubernetes.io/docs/concepts/services-networking/network-policies/) are sets of rules which define how Pods are allowed to talk to other Pods and resources inside and outside the cluster. Pods not covered by any Network Policy will continue to receive unrestricted traffic from any endpoint
- Policies can specify `podSelectors`, ingress or egress `policyTypes` and rules for `ipBlocks` and `ports`. Very simplistic default allow or default deny policies can be defined as well. As a good practice, it is recommended to define a default deny policy to block all traffic to and from the namespace.

## Monitoring, logging, and troubleshooting

For monitoring resource usage, the following tools are popular

- **Metrics server**: a cluster-wide aggregator of resource usage data
- **Prometheus**: scrape the resource usage from different Kubernetes components and objects. Using its client libraries, we can also instrument the code of our application

For logging, kubernetes can collect logs for different cluster components, but does not provide cluster-wide logging capabilities. To achieve cluster-wide logging, a popular method is to collect logs with [`ElasticSearch`](https://docs.fluentd.org/output/elasticsearch) and have [`Fluentd`](http://www.fluentd.org/) as a node agent

## Helm

A complex application deployment can bundle up its manifests into a **chart**. Charts can then be served by a package repository. The package manager that deals with charts is [`helm`](https://helm.sh/).

## Service mesh

- Service mesh is a 3rd party solution to native kubernetes application connectivity and exposure
- These solution are able to offer things like service discovery, multi-cloud routing, and traffic telemetry
- A **service mesh** is an implementation that relies on the proxy component part of the data plane

## Application deployment strategies

- **Canary**: runs two application releases simultaneously managed by two independent Deployment controllers, both exposed by the same Service
- **Blue/Green**: runs the same application release or two releases of the application on two isolated environments, but only one of the two environments is actively receiving traffic, while the second environment is idle, or may undergo rigorous testing prior to shifting traffic to it
