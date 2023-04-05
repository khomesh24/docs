# Container Scheduling

## Taints and Tolerations

Taints are applied on nodes, to avoid pod scheduling on that node. Toleration is applied on pods, this allows scheduler to schedule pod on tainted node.<br />
Taints and tolerations work together to ensure that pods are not scheduled onto inappropriate nodes. One or more taints are applied to a node; this marks that the node should not accept any pods that do not tolerate the taints.<br />

In Addition, taint and toleration can make sure pods are not schedule in a specfic node but you cannot use this to schedule node on specific node.  

There are three types of taints:<br />

**NoSchedule**: No schedule, but already running pod will keep running.<br />
**NoExecute**:  No schedule and i will evict exiting running nodes.<br />
**PreferNoSchedule**: Try to exclude the node from scheduling, but no guarantee. <br />

#### Command to set taint

```bash
kubectl traint node <node name> app=blue:NoSchedule
```

#### Pod defination to set tolerations

```yaml
apiVersion: v1
kind: pod
metadata:
  name: pod_name
spec:
  containers:
    - name: nginx
      image: nginx
  tolerations:
    - key: "app"
      operator: "Equal"
      value: "blue"
      effect: "NoSchedule"
```

**Note:** All value in toleration need to be inside double quotes. 


## Node Selector

We can use **nodeSelector** in **spec.containers.nodeSelector** to assign pod to a specific node. We can simply use label to acchive this.


#### Set a label on node

```bash
kubectl label node <node name> node=blue
```

#### Set nodeSelector on the pod definations

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: test-pod
spec:
  containers:
    - name: nginx
      image: nginx
  nodeSelector:
    node: blue
```

But nodeSelector has a limition we cannot apply complex condition using this. To achive this we use Node Affinity.


## Node Affinity

It is same as nodeSelector but this is more expressive. And give more controller to user.<br/>

There are two type of affinity.

#### requiredDuringSchedulingIgnoredDuringExecution

Pod will be schedule only if requirement is met otherwise it will stuck in pending state. And after schedule if node label for some reason pod wont be evicted.

#### preferredDuringSchedulingIgnoredDuringExecution

Scheduler will try to schedule on selected node, but if it not available or unable to schedule for some reason, pod will be schedule on some other node.


```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod_with_affinity
spec:
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: topology.kubernetes.io/zone
            operator: In
            values:
            - antarctica-east1
            - antarctica-west1
      preferredDuringSchedulingIgnoredDuringExecution:
      - weight: 1
        preference:
          matchExpressions:
          - key: another-node-label-key
            operator: In
            values:
            - another-node-label-value
  containers:
  - name: with-node-affinity
    image: registry.k8s.io/pause:2.0
```

Refer [Node Affinity](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#node-affinity) for more info. 