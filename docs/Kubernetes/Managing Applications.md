# Application life cycle

## Rolling Updates and Rollbacks

Rolling Update is a Stratagy use to update pods with downtime. We remove one old pod and replace it will newer one.<br/>

It recommended modify the deployment defination file and then apply the new changes using **kubectl apply -f <deployment defination>**.

Use below command to monitor.

```bash
kubectl rollout status deployment/name
kubectl rollout history deployment/name
kubectl rollout undo deployment/name
```

Different deployment stratagies we can use are below: 

- **Rolling deployment**: replaces pods running the old version of the application with the new version, one by one, without downtime to the cluster.
- **Recreate**: terminates all the pods and replaces them with the new version.
- **Ramped slow rollout**: rolls out replicas of the new version, while in parallel, shutting down old replicas. 
- **Best-effort controlled rollout**: specifies a “max unavailable” parameter which indicates what percentage of existing pods can be unavailable during the upgrade, enabling the rollout to happen much more quickly.
- **Canary deployment**: uses a progressive delivery approach, with one version of the application serving most users, and another, newer version serving a small pool of test users. The test deployment is rolled out to more users if it is successful.


There is also a very popular strategy called **Blue_Green**. Refer [doc](https://kubernetes.io/blog/2018/04/30/zero-downtime-deployment-kubernetes-jenkins/#blue-green-deployment) for more detailed explaination.

## Commands vs Arguments

Command override the entrypoint define in the container image and arguments is use to override CMD.
This parameter cannot be changed once pod is created. 

#### Example:

Below is the docker file for image

```yaml
FROM python:3.6-alpine

RUN pip install flask

COPY . /opt/

EXPOSE 8080

WORKDIR /opt

ENTRYPOINT ["python", "app.py"]

CMD ["--color", "red"]
```

Pod defination

```yaml
apiVersion: v1 
kind: Pod 
metadata:
  name: webapp-green
  labels:
      name: webapp-green 
spec:
  containers:
  - name: simple-webapp
    image: webapp-color
    command: ["python", "app.py"]
    args: ["--color", "pink"]
```

args in pod defination will override the CMD in image.
And we set command to **["python3", "app.py"]** in defination then it will override entrypoint in image.<br/>
So in our case when pod is created it will run **python app,py --color pink**


## Passing env to pods

Example 1: 

```yaml
spec:
  containers:
    - name: nginx
      image: nginx
      env:
        - name: Key_name
          value: Key_value
```

Example 2: Use all key-value pair in env

```yaml
spec:
  containers:
    - name: nginx
      image: nginx
      envFrom:
        - configMapRef:
            name: <configMap_name>
```

Example 3: Use very specific key-value pair as env

```yaml
spec:
  containers:
    - name: nginx
      image: nginx
      env:
        - name: <keyname>
          valueFrom:
            configMapKeyRef:
              name: <configMap_name>
              key: <key in config map>
```

Example 4:

```yaml
spec:
  containers:
    - name: nginx
      image: nginx
      envFrom:
        - secretKeyRef:
            name: <secret_name>
```


## ConfigMaps

Create a key value pair do store data and 


## Secrets

### Encrying secret data at rest

