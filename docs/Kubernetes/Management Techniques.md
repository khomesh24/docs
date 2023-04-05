# Kubernetes Management Techniques
----------------------------------

## Imperative Commands 

When using imperative commands, a user operates directly on live objects in a cluster. The user provides operations to the kubectl command as arguments or flags.

This is the recommended way to get started or to run a one-off task in a cluster. Because this technique operates directly on live objects, it provides no history of previous configurations.

```bash
kubectl create deployment nginx --image nginx
kubectl run nginx --image nginx
```

## Imperative object configuration 

In imperative object configuration, the kubectl command specifies the operation (create, replace, etc.), optional flags and at least one file name. The file specified must contain a full definition of the object in YAML or JSON format.

```bash
kubectl create -f nginx.yaml
kubectl delete -f nginx.yaml -f redis.yaml
kubectl replace -f nginx.yaml
```

## Declarative object configuration

In declarative object configuration, a user operates on object configuration files stored locally, however the user does not define the operations to be taken on the files. Create, update, and delete operations are automatically detected per-object by kubectl. This enables working on directories, where different operations might be needed for different objects.

```bash 
kubectl diff -f configs/
kubectl apply -f configs/

# Recursively process directories:

kubectl diff -R -f configs/
kubectl apply -R -f configs/
```