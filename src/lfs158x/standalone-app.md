# Deploying a stand-alone application

## Deploy

### Dashboard

1) Accessing the dashboard
  - Ensure the cluster is running with `minikube start` and `minikube status`
  - Open the dashboard with `minikube dashboard`
2) Create the application
  - Use the form and use the following fields
    - name: `web-dash`
    - image: `nginx`
    - replica count: 1
    - service: External
    - port: 8080
    - target port: 80
    - Protocol: TCP
  - Used advanced options for setting labels and namespaces
  - Click *Deploy* to trigger the deployment
3) Checking resources
  - Use resource navigation to display details of deployments, replica sets, and pods in the `default` namespace
  - Dashboard should display a one-to-one match of `kubectl` responses

### Command line

1) Create a manifest for a deployment controller

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: webserver
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:alpine
        ports:
        - containerPort: 80
```

2) Create the resource using the `kubectl create` command with the `-f` to specify the manifest file

```shell
$ kubectl create -f webserver.yaml
```

3) List the resources to verify their creation

Alternatively, an application can be deployed imperatively as follows

```shell
$ kubectl create deployment webserver --image=nginx:alpine --replicas=3 --port=80
```

## Expose

1) Create a manifest for a service type to expose the nginx image

```yaml
apiVersion: v1
kind: Service
metadata:
  name: web-service
  labels:
    app: nginx
spec:
  type: NodePort
  ports:
  - port: 80
    protocol: TCP
  selector:
    app: nginx 
```

2) Use `kubectl` to create the service

```shell
$ kubectl create -f webserver-svc.yaml
```

Alternatively, we can expose a previously created deployment (must already exist) with the following command

```shell
$ kubectl expose deployment webserver --name=web-service --type=NodePort
```

## Access

1) Get the IP address of the `minikube` VM node with `minikube ip`
2) Find the node port of the service with `kubectl describe service web-service` and looking at the `NodePort` filed
3) Open the webpage `http://{ip}/{nodeport}` and you should see the welcome to Nginx page

## Liveness and Readiness

- At times, applications become unresponsive or delayed during startup
- Liveness and readiness probes allow `kubelet` to control the health of an application running inside a pod's container
  - `kubelet` can force a restart of an unresponsive application
  - Readiness should fail a few times before a liveness check forces a restart

### Liveness probes

- If a container in the Pod has been running successfully for a while, but the application running inside this container suddenly stopped responding to our requests, then that container is no longer useful to us.
- In such cases, the recommended course of action is to restart the container.
- To avoid restarting manually, use a liveness probe to perform a health check on an application. If the health check fails, `kubelet` can restart the affected container automatically

> The liveness command below checks for the existence of the file `/tmp/healthy`

```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    test: liveness
  name: liveness-exec
spec:
  containers:
  - name: liveness
    image: k8s.gcr.io/busybox
    args:
    - /bin/sh
    - -c
    - touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600
    livenessProbe:
      exec:
        command:
        - cat
        - /tmp/healthy
      initialDelaySeconds: 15
      failureThreshold: 1
      periodSeconds: 5
```

- File existence is checked every 5 seconds as defined by the `periodSeconds` field
- The `initialDelaySeconds` tells `kubelet` to wait 15 seconds before the first probe runs
- The `failureThreshold` determines how many probe failures would instruct `kubelet` to declare a container unhealthy

> The HTTP liveness probe can be used to send an HTTP request to a specified endpoint

```yaml
  livenessProbe:
       httpGet:
         path: /healthz
         port: 8080
         httpHeaders:
         - name: X-Custom-Header
           value: Awesome
       initialDelaySeconds: 15
       periodSeconds: 5
```

> The TCP liveness probe attempts to open the TCP Socket to the container which is running the application

```yaml

  livenessProbe:
      tcpSocket:
        port: 8080
      initialDelaySeconds: 15
      periodSeconds: 5
```

### Readiness probes

- Sometimes, applications need to meet certain conditions before being ready to serve traffic
- Use readiness probes to wait for certain conditions to occur before serving traffic
- Pods with containers that do not report a ready status will not receive traffic

```yaml
  readinessProbe:
          exec:
            command:
            - cat
            - /tmp/healthy
          initialDelaySeconds: 5 
          periodSeconds: 5
```
