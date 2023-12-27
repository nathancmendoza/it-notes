# ConfigMaps and secrets

## ConfigMaps

- Allows decoupling of configuration details from a container image
- Configuration data is passed as key-value pairs
- Consumed by pods, or other system components, in the form of environment variables, commands or volumes

### Creating configMaps

#### From literal values

Create a ConfigMap with the `kubectl create` command

```shell
$ kubectl create configmap my-config \
  --from-literal=key1=value1 \
  --from-literal=key2=value2

configmap/my-config created
```

Display the ConfigMap details with the `kubectl get configmaps` command

```shell
$ kubectl get configmaps my-config -o yaml

apiVersion: v1
data:
  key1: value1
  key2: value2
kind: ConfigMap
metadata:
  creationTimestamp: 2022-04-02T07:21:55Z
  name: my-config
  namespace: default
  resourceVersion: "241345"
  selfLink: /api/v1/namespaces/default/configmaps/my-config
  uid: d35f0a3d-45d1-11e7-9e62-080027a46057

```

#### From a definition manifest

Create a definition file `customer1-configmap.yaml` with the following contents

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: customer1
data:
  TEXT1: Customer1_Company
  TEXT2: Welcomes You
  COMPANY: Customer1 Company Technology Pct. Ltd.

```

Pass the name of the file shown above to the `kubectl create` command.

```shell
$ kubectl create -f customer1-configmap.yaml

configmap/customer1 created
```

#### From a file

Create the file `permission-reset.properties` with the following contents

```plaintext
permission=read-only
allowed="true"
resetCount=3
```

Use the following command to create the ConfigMap

```shell
$ kubectl create configmap permission-config \
  --from-file=<path/to/>permission-reset.properties

configmap/permission-config created
```

### Using configMaps

#### As environment variables

> Inside a Container, we can retrieve the key-value data of an entire ConfigMap or the values of specific ConfigMap keys as environment variables.

- Entire ConfigMap

```yaml
...
  containers:
  - name: myapp-full-container
    image: myapp
    envFrom:
    - configMapRef:
      name: full-config-map
...
```

- Specific keys

```yaml
...
  containers:
  - name: myapp-specific-container
    image: myapp
    env:
    - name: SPECIFIC_ENV_VAR1
      valueFrom:
        configMapKeyRef:
          name: config-map-1
          key: SPECIFIC_DATA
    - name: SPECIFIC_ENV_VAR2
      valueFrom:
        configMapKeyRef:
          name: config-map-2
          key: SPECIFIC_INFO
...
```

#### As volumes

> We can mount a vol-config-map ConfigMap as a Volume inside a Pod. For each key in the ConfigMap, a file gets created in the mount path (where the file is named with the key name) and the respective key's value becomes the content of the file:

```yaml
...
  containers:
  - name: myapp-vol-container
    image: myapp
    volumeMounts:
    - name: config-volume
      mountPath: /etc/config
  volumes:
  - name: config-volume
    configMap:
      name: vol-config-map
...
```

## Secrets

- Say a web frontend connects to a MySQL database using a password. That password could be specified in the deployment manifest, but would be unprotected
- The `Secret` object can encode sensitive information before sharing it. These objects are *referenced* without exposing the contents
- **Important**: By default, secret data is stored as plaintext inside `etcd`. Administrators must limit access to the API server and `etcd` or enable encryption at rest for `etcd`

### Creating secrets

#### From literal values

Create a secret with the `kubectl create secret` command and pass it the secret data to be stored

```shell
$ kubectl create secret generic my-password \
  --from-literal=password=mysqlpassword
```

Analyze the secret with the `get` and `describe` commands. Notice that they do not reveal the contents of the secret

```shell
$ kubectl get secret my-password

NAME          TYPE     DATA   AGE
my-password   Opaque   1      8m

$ kubectl describe secret my-password

Name:          my-password
Namespace:     default
Labels:        <none>
Annotations:   <none>

Type Opaque

Data
====
password: 13 bytes
```

#### From definition manifest

Create the manifest definition `mypass.yaml` with the following contents

- The `data` secret type is [base64](https://en.wikipedia.org/wiki/Base64) encoded
- Note that base64 is **not** encrypted and can be decoded by anyone easily
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: my-password
type: Opaque
data:
  password: bXlzcWxwYXNzd29yZAo=
```

- The `stringData` secret can be left as-is. The value will be encoded when the secret is created

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: my-password
type: Opaque
stringData:
  password: mysqlpassword
```

Using the `mypass.yaml` definition file we can now create a secret with `kubectl create` command

```shell
$ kubectl create -f mypass.yaml

secret/my-password created
```

> Take care with secrets defined with manifest files. Make sure that you do **not** commit secret definition files into source control

#### From a file

To create a secret from a file: 

1) Encode the sensitive data and write the encoded data to a text file
2) Create the secret from the text file using the `kubectl create` command

```shell
$ echo -n mysqlpassword | base64 > password.txt

$ kubectl create secret generic my-file-password \
  --from-file=password.txt

secret/my-file-password created
```

Analyze the secret with the `get` and `describe` commands. Notice the contents of the secret are not revealed

```shell
$ kubectl get secret my-file-password

NAME               TYPE     DATA   AGE 
my-file-password   Opaque   1      8m

$ kubectl describe secret my-file-password

Name:          my-file-password
Namespace:     default
Labels:        <none>
Annotations:   <none>

Type  Opaque

Data
====
password.txt:  13 bytes
```

### Using secrets

Secrets are consumed by Containers in Pods as mounted data volumes, or as environment variables, and are referenced in their entirety or specific key-values.

#### As environment variables

Below we reference only the password key of the `my-password` Secret and assign its value to the `WORDPRESS_DB_PASSWORD` environment variable

```yaml
....
spec:
  containers:
  - image: wordpress:4.7.3-apache
    name: wordpress
    env:
    - name: WORDPRESS_DB_PASSWORD
      valueFrom:
        secretKeyRef:
          name: my-password
          key: password
....
```

#### As volumes

We can also mount a Secret as a Volume inside a Pod. The following example creates a file for each my-password Secret key (where the files are named after the names of the keys), the files containing the values of the respective Secret keys

```yaml
....
spec:
  containers:
  - image: wordpress:4.7.3-apache
    name: wordpress
    volumeMounts:
    - name: secret-volume
      mountPath: "/etc/secret-data"
      readOnly: true
  volumes:
  - name: secret-volume
    secret:
      secretName: my-password
....
```
