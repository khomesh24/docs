# Minikube

### **Installation**

### **Start cluster**

```bash
minikube start [flags]
```

List of all [Flags](https://minikube.sigs.k8s.io/docs/commands/start/)

### **Manage Cluster**

Pause Kubernetes without impacting deployed applications:
```bash
minikube pause
```

Unpause a paused instance:
```bash
minikube unpause
```

Halt the cluster:
```bash
minikube stop
```

Change the default memory limit (requires a restart):
```bash
minikube config set memory 9001
```

Browse the catalog of easily installed Kubernetes services:
```bash
minikube addons list
```

Create a second cluster running an older Kubernetes release:
```bash
minikube start -p aged --kubernetes-version=v1.16.1
```

Delete all of the minikube clusters:
```bash 
minikube delete --all
```

### **Interact**

We can use kubectl to access cluster

```bash
kubectl get all
```

Alternatively, you can use minikube to interact with cluster

```bash
minikube kubectl -- get pods
```

Create a alias to make things easier

```bash
alias kubectl='minikube kubectl'
```

To access kubernetes dashboard 

```bash
minikube dashboard
```

### **Deploy Applications**

#### Service

Create a sample deployment and expose it on port 8080:

```bash
kubectl create deployment hello-minikube --image=kicbase/echo-server:1.0
kubectl expose deployment hello-minikube --type=NodePort --port=8080
```

It may take a moment, but your deployment will soon show up when you run:

```bash
kubectl get services hello-minikube
```

The easiest way to access this service is to let minikube launch a web browser for you:

```bash
minikube service hello-minikube
```

Alternatively, use kubectl to forward the port:

```bash 
kubectl port-forward service/hello-minikube 7080:8080
```
Tada! Your application is now available at http://localhost:7080/.


#### LoadBalancer

To access a LoadBalancer deployment, use the “minikube tunnel” command. Here is an example deployment:

```bash
kubectl create deployment balanced --image=kicbase/echo-server:1.0
kubectl expose deployment balanced --type=LoadBalancer --port=8080
```

In another window, start the tunnel to create a routable IP for the ‘balanced’ deployment:

```bash
minikube tunnel
```
To find the routable IP, run this command and examine the EXTERNAL-IP column:

```bash
kubectl get services balanced
```

Your deployment is now available at <EXTERNAL-IP>:8080

#### Ingress

Enable ingress addon:

minikube addons enable ingress
The following example creates simple echo-server services and an Ingress object to route to these services.

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: foo-app
  labels:
    app: foo
spec:
  containers:
    - name: foo-app
      image: 'kicbase/echo-server:1.0'
---
kind: Service
apiVersion: v1
metadata:
  name: foo-service
spec:
  selector:
    app: foo
  ports:
    - port: 8080
---
kind: Pod
apiVersion: v1
metadata:
  name: bar-app
  labels:
    app: bar
spec:
  containers:
    - name: bar-app
      image: 'kicbase/echo-server:1.0'
---
kind: Service
apiVersion: v1
metadata:
  name: bar-service
spec:
  selector:
    app: bar
  ports:
    - port: 8080
---
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: example-ingress
spec:
  rules:
    - http:
        paths:
          - pathType: Prefix
            path: /foo
            backend:
              service:
                name: foo-service
                port:
                  number: 8080
          - pathType: Prefix
            path: /bar
            backend:
              service:
                name: bar-service
                port:
                  number: 8080
```

Apply the contents

```bash
kubectl apply -f <resource defination.yaml>
```

Wait for ingress address

```bash
kubectl get ingress

NAME              CLASS   HOSTS   ADDRESS          PORTS   AGE
example-ingress   nginx   *       <your_ip_here>   80      5m45s
```

##### Note for Docker Desktop Users:
To get ingress to work you’ll need to open a new terminal window and run minikube tunnel and in the following step use 127.0.0.1 in place of <ip_from_above>.

Now verify that the ingress works

```bash
$ curl <ip_from_above>/foo
Request served by foo-app
...
```

```bash
$ curl <ip_from_above>/bar
Request served by bar-app
...
```