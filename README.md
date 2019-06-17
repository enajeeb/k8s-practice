# k8s-practice
Kubernetes practice

## Misc
```
# get all namespaces
kubectl get ns

# get pods in kubernetes ns
kubectl get pods -n kube-system
```

## PODS
```
# run one nginx pod (imperative)
kubectl run k8-nginx --image=nginx --port=80

# get status
kubectl get all

# get pod name
kubectl get pods -o go-template --template '{{range .items}}{{.metadata.name}}{{"\n"}}{{end}}'

# start porxy
kubectl proxy

# proxy to pod
http://localhost:8001/api/v1/namespaces/default/pods/k8-nginx-59ff48f654-ww24h/proxy/

# delete deployment
kubectl delete deployment.apps/k8-nginx
```

```
# log into pod
kubectl exec -it <Podname> bash
```

```
# Logs
kubectl logs -f <Podname>
```


## Replication Controller
```
kubectl apply -f replication-controller.yaml

# increase pods from 5 to 10
kubectl apply -f replication-controller.yaml

kubectl get pods -o go-template --template '{{range .items}}{{.metadata.name}}{{"\n"}}{{end}}'
kubectl get pods -o custom-columns=NAME:.metadata.name

kubectl get all

kubectl scale --replicas=5 rs/k8-nginx-rc

kubectl delete -f replication-controller.yaml
```
## Service
```
# start service (imperative)
kubectl expose rc k8-nginx-rc --name=k8-nginx-svc --port=80 --target-port=80 --type=NodePort --dry-run=true

kubectl describe svc k8-nginx-svc

export NODE_PORT=$(kubectl get services/k8-nginx-svc -o go-template='{{(index .spec.ports 0).nodePort}}')
echo NODE_PORT=$NODE_PORT

# test service using
# http://localhost:$NODE_PORT

```

## Deployment
```
kubectl get deployments --all-namespaces
kubectl delete -n <NAMESPACE> deployment <DEPLOYMENT>
kubectl delete -n default deployment nginx-deployment

kubectl delete pods --all
kubectl delete pods,services -l app=nginx

kubectl describe deployments
kubectl rollout status deployment.apps/nginx-deployment
kubectl get deployment.apps/nginx-deployment -o yaml

# old way
kubectl run --generator=deployment/v1beta1 nginx --image=nginx -o yaml --dry-run=true

# new way
kubectl create deployment nginx --image=nginx:1.16 -o yaml --dry-run=true
kubectl scale --replicas=5 deployment/nginx
```

## Namespace
```
kubectl get namespaces
kubectl get pods --all-namespaces
kubectl get pods --namespace=default

# switch to namespace
kubectl config set-context $(kubectl config current-context) --namespace=kube-system
# start using context
kubectl config use-context kube-system
```

## Machine setup
```
alias k='kubectl'
```