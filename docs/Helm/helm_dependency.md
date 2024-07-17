#### Helm Dependency

There are lot of pre-define helm charts. So using dependencies we can directly import those charts and override the variables according to our needs.

Declare helm charts 

```
# Charts.yaml

dependencies:
  - name: ngnix
    version: "1.2.3"
    repository: "https://example.com/charts"
  - name: prometheus
    version: "4.4.5"
    repository: "https://example2.com/charts"
```

### Dependency list

```
helm dependency ls
```

### Dependency build

Build is used to reconstruct a chart's dependencies to the state specified in the lock file. This will not re-negotiate dependencies, as 'helm dependency update' does. If no lock file is found, 'helm dependency build' will mirror the behavior of `helm dependency update`.

```
helm dependency build <chart>
```

### Dependency update

Build will construct chart in `charts` directory in parent chart. 

```
helm dependency update <chart>
```
