# Commands

```bash
kubectl set image resource/name container_name=new_image
```

kubectl rollout status deployment/name

kubectl rollout history deployment/name

kubectl rollout undo deployment/name


Switch namespace: 

```bash 
kubectl config set-context --current --namespace=<insert-namespace-name-here>
kubectl config view --minify | grep namespace:
```