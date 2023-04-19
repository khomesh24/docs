# Core Kubernetes Concept
-------------------------

## Pods

Pods are the smallest deployable units of computing that you can create and manage in Kubernetes.
A Pod (as in a pod of whales or pea pod) is a group of one or more containers, with shared storage and network resources, and a specification for how to run the containers. 

The shared context of a Pod is a set of Linux namespaces, cgroups, and potentially other facets of isolation - the same things that isolate a container. Within a Pod's context, the individual applications may have further sub-isolations applied.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: test-pod
  labels:
    app: nginx
spec:
  containers:
    - name: nginx-container
      image: nginx
    - name: busybox-container
      image: busybox
```


## ReplicaSet

A ReplicaSet's purpose is to maintain a stable set of replica Pods running at any given time. As such, it is often used to guarantee the availability of a specified number of identical Pods.

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: rs-app
  labels:
    app: rc-label
spec:
  template:
    metadata:
      name: pod-busybox
      labels:
        app: pod-label
    spec:
      containers:
        - name: busybox
          image: nginx
  replicas: 2
  selector:
    matchLabels:
      app: pod-label
```

ReplicaSet use label to identify the pods. It will use container defination in template to scale new pods.

We can also use `kubectl scale` to modify replicas.

`kubectl scale --replicas <num> <replicationset resource>`

## DaemonSet

DaemonSet is similar to ReplicaSet but it will maintain one replica on each nodes. When a node is added to cluster DaemonSet will create a replica on in and remove when node is deleted. This is mainly use for monitering and logging purposes. It used NodeAffinity to schedule and pods on node. 

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: elasticsearch
  labels:
    app: monitering
spec:
  template:
    metadata:
      labels:
        app: pod-fluentd
    spec:
      containers:
        - name: fluentd
          image: fluentd
  selector:
    matchLabels:
      app: pod-fluentd
```

## Deployment

Deployment is like a orchestration tool, we can used this manage pods/replicas/rolling update and other operations efficiently. 

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: rs-app
  labels:
    app: rc-label
spec:
  template:
    metadata:
      name: pod-busybox
      labels:
        app: pod-label
    spec:
      containers:
        - name: busybox
          image: nginx
  replicas: 2
  selector:
    matchLabels:
      app: pod-label
```

## Services

Service is a method for exposing a network application that is running as one or more Pods in your cluster

For example, consider a stateless image-processing backend which is running with 3 replicas. Those replicas are fungible—frontends do not care which backend they use. While the actual Pods that compose the backend set may change, the frontend clients should not need to be aware of that, nor should they need to keep track of the set of backends themselves.

The Service abstraction enables this decoupling.
It used selector to find the endpoint.


#### Nodeport
Nodeport uses system port to route the external traffic to pod.

nodeport: System(worker node) port. Valid range: 30000-32767 <br />
port: Service port <br />
targetport: pod port

```yaml
apiVersion: v1
kind: Service
metadata:
  name: Service name 
spec:
  type: NodePort
  ports:
    - port:
      nodePort:
      targetPort: 
  selector:
    <label key>: <label value> 
```

#### ClusterIP

Here we create a virtual IP and map all pod of same type to it. We use labels to mapd pods.
ClusterIp will loadbalance the request and route to any of the available pod.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: Service name 
spec:
  type: ClusterIP
  ports:
    - port:
      targetPort: 
  selector:
    <label key>: <label value> 
```
 Note: if type is not define that it will by default conside it as ClusterIP 

#### Loadbalances


### Namespaces

Namespaces provides a mechanism for isolating groups of resources within a single cluster. Names of resources need to be unique within a namespace, but not across namespaces. 

Creating a namespace: 

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: test-namespace
```
```bash
kubectl create -f <test-namespace.yaml>
```

Switch namespace: 

```bash 
kubectl config set-context --current --namespace=<insert-namespace-name-here>
kubectl config view --minify | grep namespace:
```

DNS entry maintain below pattern `<service-name>.<namespace-name>.svc.cluster.local`

