# Static Pods

Static Pod that are directly manage by kubelet daemon without the API server observing them. <br/>
Static Pods are always bound to one Kubelet on a specific node. <br/>

The kubelet automatically tries to create a mirror Pod on the Kubernetes API server for each static Pod. This means that the Pods running on a node are visible on the API server, but cannot be controlled from there. The Pod names will be suffixed with the node hostname with a leading hyphen. <br/>

**Finding Static pods defination**

On the node look for **--config** in kubelet service file.

```bash
$ systemctl cat kubelet  | grep config
[Unit]
Description=kubelet: The Kubernetes Node Agent

[.....]

[Service]
Environment="KUBELET_KUBECONFIG_ARGS=--bootstrap-kubeconfig=/etc/kubernetes/bootstrap-ku>
Environment="KUBELET_CONFIG_ARGS=--config=/var/lib/kubelet/config.yaml"                    <<<<< 
# This is a file that "kubeadm init" and "kubeadm join" generates at runtime, populating>
EnvironmentFile=-/var/lib/kubelet/kubeadm-flags.env
# This is a file that the user can use for overrides of the kubelet args as a last resor>
# the .NodeRegistration.KubeletExtraArgs object in the configuration files instead. KUBE>
EnvironmentFile=-/etc/default/kubelet
ExecStart=
ExecStart=/usr/bin/kubelet $KUBELET_KUBECONFIG_ARGS $KUBELET_CONFIG_ARGS $KUBELET_KUBEAD>
```

And in the **--config** file look for **staticPodPath**

```bash
$ grep ^static /var/lib/kubelet/config.yaml 
staticPodPath: /etc/kubernetes/manifests
```

In above case, **/etc/kubernetes/manifests** is the directory where statis pod defination are stored.<br/>
We can create our own pod defination in here and kubelet will start and manage this pod.