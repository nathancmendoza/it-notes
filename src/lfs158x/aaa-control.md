# Authentication, authorization, admission control

- **Authentication**: authenticate a user based on credentials provided as part of API requests
- **Authorization**: authorizes the API requests submitted by the authenticated user
- **Admission control**: software modules that validate and/or modify user requests

## Authentication

Kubernetes supports 2 kinds of users

- **Normal users**: managed outside of the kubernetes cluster via independent services
- **Service accounts**: allow in-cluster processes to communicate with the API server to perform various operations

Kubernetes utilizes authentication modules to authenticate users

- **X509 Client Certificates**: To enable client certificate authentication, we need to reference a file containing one or more certificate authorities by passing the `--client-ca-file=SOMEFILE` option to the API server. The certificate authorities mentioned in the file would validate the client certificates presented by users to the API server.
- **Static Token File**: We can pass a file containing pre-defined bearer tokens with the `--token-auth-file=SOMEFILE` option to the API server. Currently, these tokens would last indefinitely, and they cannot be changed without restarting the API server.
- **Bootstrap Tokens**: Tokens used for bootstrapping new Kubernetes clusters.
- **Service Account Tokens**: Automatically enabled authenticators that use signed bearer tokens to verify requests. These tokens get attached to Pods using the Service Account Admission Controller, which allows in-cluster processes to talk to the API server.
- **OpenID Connect Tokens**: OpenID Connect helps us connect with OAuth2 providers, such as Azure Active Directory, Salesforce, and Google, to offload the authentication to external services.
- **Webhook Token Authentication**: With Webhook-based authentication, verification of bearer tokens can be offloaded to a remote service.
- **Authenticating Proxy**: Allows for the programming of additional authentication logic.

Multiple authentication modules can be used. The first one to succeed short-circuits the evaluation

## Authorization

After successfully authenticating, users can send API requests to perform different operations. However, each request needs to be **authorized** that decides if the request is allowed or denied.

### Node

- A special-purpose authorization mode which specifically authorizes API requests made by kubelets
- Allows read operations for services, endpoints, or nodes, and writes operations for nodes, pods, and events

### Attribute-based access control

- Grants access to API request based on policies with attributes
- Enabled by starting the API server with `--authorization-mode=ABAC` and specifying the policy with `authorization-policy-file=PolicyFile.json.`

```json
{
  "apiVersion": "abac.authorization.kubernetes.io/v1beta1",
  "kind": "Policy",
  "spec": {
    "user": "bob",
    "namespace": "lfs158",
    "resource": "pods",
    "readonly": true
  }
}
```

> This policy only allows user `bob` to *read* **pods** in the *namespace* `lfs158`

### Webhook

- Kubernetes requests authorization decisions to be made by third-party services
- Services return `true` for successful authorization and `false` for failure
- Enabled by starting the API server with `--authorization-webhook-config-file=SOME_FILENAME`

### Role-based access control

- Regulates access to resources based on the *roles* of individual users
- Multiple roles can be attached to subjects
- Roles are created by restricting access by specific operations (called verbs) like `create`, `get`, `update`, `patch`
- Kubernetes allows 2 kinds of roles to be created
  1) **Role**: grants access to resources within a specific namespace
  2) **Cluster Role**: grants the same permissions as a role, but has a cluster-wide scope
- Enabled by starting the API server with `--authorization-mode=RBAC` and dynamically configuring policies

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: lfs158
  name: pod-reader
rules:
- apiGroups: [""] # "" indicates the core API group
  resources: ["pods"]
  verbs: ["get", "watch", "list"]
```

The manifest above defines a `pod-reader` role, which can only *read* pods of the `lfs158` namespace. With the role created, it can be **bound** to users using a `RoleBinding` object, of which there are 2

- `RoleBinding`: allows binding users to the same namespace of the role
- `ClusterRoleBinding`: allows granting access to resources at the cluster-level and to **all** namespaces

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: pod-read-access
  namespace: lfs158
subjects:
- kind: User
  name: bob
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```

The manifest above defines a role binding between the `pod-reader` role and the `bob` user.

## Access control

- Used to specify granular access control policies, such as allowing privileged containers or checking on resource quotas
- Policies are enforced by the user of [admission controllers](https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/) and take effect only when API requests are authenticated and authorized
- To use admission controls, start the API server with the `--enable-admission-plugins` options, which accepts a comma-delimited ordered list of controller names
