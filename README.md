# k8s-practice
Kubernetes practice

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

kubectl delete -f replication-controller.yaml
```
## Service
```
# start service (imperative)
kubectl expose rc k8-nginx-rc --name=k8-nginx-svc --port=80 --target-port=80 --type=NodePort --dry-run=true

kubectl describe svc k8-nginx-svc
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
```