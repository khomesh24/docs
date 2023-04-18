# Managing Scheduler 

## Scheduler

Kubernetes has default scheduler in kube-system namespace.
We can add a custom namespace depending on our requirement.

To user custom scheduler you can use **schedulerName** parameter in pod specs.

```yaml
apiVersion: v1 
kind: Pod 
metadata:
  name: nginx 
spec:
  containers:
  - image: nginx
    name: nginx
  schedulerName: my-scheduler
```

## Scheduling Process

Scheduling Queue  ->  Filtering  -> Scoring -> Binding

**Scheduling Queue**: Here all scheduling request are collected and sorted baised on priority. We use PrioritySort plugin to acchive this.

**Filtering**: We filter out the node that are incapable to deploy given pod. It uses NodeResourceFit/NodeName/nodeUnschedulable plugins to filter out nodes.

**Scoring**: This will give score to filtered node based on resource and resource stress on the node. More the stress lower the scores. It used NodeResourceFit/ImageLocality plugin to find the best fit.

**Binding**: Depending on score binder will schedule node on best fit node. It uses binder plugin to schedule the node.


Refer [Advanced scheduling](https://kubernetes.io/blog/2017/03/advanced-scheduling-in-kubernetes/)