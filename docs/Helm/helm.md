# Helm 
------
The package manager for Kubernetes


### Manager helm repo

Add new repo 

```
helm repo add helm-public-repos <repo url> --username <> --password <>
```

List repo list
```
helm repo list
```

Search helm in repo 

```
helm search repo <name>
```


### Creating a new helm chart

```
helm create <name>
```

Its create a chart directory with file and directory used in charts.

```
new-chart
├── Chart.yaml                      # Information about chart
├── charts                          # Stores dependant charts 
├── templates                       # Contains the resource defination of charts
│   ├── NOTES.txt
│   ├── _helpers.tpl
│   ├── deployment.yaml
│   ├── hpa.yaml
│   ├── ingress.yaml
│   ├── service.yaml
│   ├── serviceaccount.yaml
│   └── tests                       # Test file 
│       └── test-connection.yaml
└── values.yaml                     # Default values for var used in templates
```

### Render Helm Templates

locally render templates

```
helm templates <name> <chart>
```

### Install Charts

Apply charts to k8s cluster.
It basically render and then use `kubectl apply` the templates it generate. 

```
helm install <name> <chart path>
```

### List helm

List all helm that are applied to k8s cluster.

```
helm ls 
```

### Upgrade Helm 

Upgrade a release. When we change helm template or want to override new values, we run helm upgrade. This will generate new manifeast and run `kubectl apply`. 

Note: This could terminate all pod workload and recreate it that could cause downtime.


### Uninstall a release

Remove release 

```
helm uninstall <name>
```

