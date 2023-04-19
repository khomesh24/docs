# Kubernetes Architecture
-------------------------

Kubernetes mainly consist of Master and worker node. Master will host all control plane service where as worker host all the workload. We usualy need more that one master for HA and we can have n number of worker depending the the requirement.

![Kubernetes Architecture](https://d33wubrfki0l68.cloudfront.net/2475489eaf20163ec0f54ddc1d92aa8d4c87c96b/e7c81/images/docs/components-of-kubernetes.svg "Kubernetes Architecture")

### Components

* **Kube API server** <br/>
The API server is a component of the Kubernetes control plane that exposes the Kubernetes API. The API server is the front end for the Kubernetes control plane. 

* **ETCd** <br/>
Consistent and highly-available key value store used as Kubernetes' backing store for all cluster data.

* **Scheduler** <br/>
Control plane component that watches for newly created Pods with no assigned node, and selects a node for them to run on.  <br/>
Factors taken into account for scheduling decisions include: individual and collective resource requirements, hardware/software/policy constraints, affinity and anti-affinity specifications, data locality, inter-workload interference, and deadlines.

* **Controller Manager** <br/>
Control plane component that runs controller processes. <br/>
Logically, each controller is a separate process, but to reduce complexity, they are all compiled into a single binary and run in a single process. <br/>
Some types of these controllers are:

    __Node controller__: Responsible for noticing and responding when nodes go down. <br/>
    __Job controller__: Watches for Job objects that represent one-off tasks, then creates Pods to run those tasks to completion. <br/>
    __EndpointSlice controller__: Populates EndpointSlice objects (to provide a link between Services and Pods). <br/>
    __ServiceAccount controller__: Create default ServiceAccounts for new namespaces. <br/>

* **Cloud Controller manager** <br/>
Cloud manager is use to interact with cloud provider. It help you perform scale in/out, route creation, managing cloud provided loadbalancer and manymore. <br/>

* **Kubelet**  <br/>
Kubelet run on worker nodes. Kubelet manages kubernetes objects(pods/replicas/deployment, etc) on worker node. It interact with kubeapi service and fetch the details of pods or object schedule to run on that worker and ensure that the object is healthy.

* **Kubeproxy** <br/>
kube-proxy is a network proxy that runs on each worker node in your cluster. It maintains the network rule on the node to allow network communication to your Pods from network inside or outside of your cluster. <br/>
kube-proxy uses the operating system packet filtering layer if there is one and it's available. Otherwise, kube-proxy forwards the traffic itself.



