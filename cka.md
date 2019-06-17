# Certified Kubernetes Administrator (CKA)

* https://www.cncf.io/certification/cka/
* https://kodekloud.com/p/certified-kubernetes-administrator-with-practice-tests
* https://linuxacademy.com/linux/training/course/name/cloud-native-certified-kubernetes-administrator-cka

## Readiness Checklist
* Udemy Course
  * [ ] https://www.udemy.com/certified-kubernetes-administrator-with-practice-tests/
* Kubernetes Up and Running Book
  * [ ] https://go.heptio.com/rs/383-ENX-437/images/Kubernetes_Up_and_Running.pdf?mkt_tok=eyJpIjoiTXpKalpHSTRabUk0WmpOaSIsInQiOiJqekdpSFc0WXRHYlpJMWdQeFpiOGtqXC8yUEo2VVwvV0JVYllMWjhYb1YyVEI3RTlNYnl5NnFyK3cwQ1YyK2J5Y2ZoQ1wvbU5wNmp3WWllWnI3eUNjRTFkK1N4NU52UHo5ZGRlZFJHRjIxTVwvZHVcL0Fab0RNR3Q1RjVxRWZHZG1ZYTNkIn0%3D
* Review Curriculum 
  * [ ] https://github.com/cncf/curriculum
* kubernetes.io
  * [ ] https://kubernetes.io/docs/concepts/
  * [ ] https://kubernetes.io/docs/tasks/
  * [ ] https://kubernetes.io/docs/tutorials/
  * [ ] https://kubernetes.io/docs/reference/
* Practice
  * [ ] https://github.com/dgkanatsios/CKAD-exercises
  * [ ] https://github.com/kelseyhightower/kubernetes-the-hard-way/blob/master/README.md
  * [ ] https://github.com/walidshaari/Kubernetes-Certified-Administrator/blob/master/README.md

## Tasks
### Start pod and tie with service
```
kubectl run nginx --image=nginx:1.16 --labels="app=frontend,color=blue"
kubectl create service nodeport nginx --tcp=80:80 --node-port=30080
# update selector app=frontend
kubectl edit service nginx

# test
curl http://localhost:30080
```

## Reference
### Generators
* https://kubernetes.io/docs/reference/kubectl/conventions/

### Pods
```
# deploy a pod
kubectl run nginx --generator=run-pod/v1 --image=nginx:1.16 --dry-run=true -o yaml
kubectl run --generator=run-pod/v1 redis --image=redis:alpine --labels=tier=db

# find pods with label bu=finance
kubectl get pods --selector bu=finance
kubectl get all --selector env=prod,bu=finance,tier=frontend

# without header
kubectl get pods -o wide --no-headers

# extract specific fields
kubectl get pods -o go-template --template '{{range .items}}{{.metadata.name}}{{"\n"}}{{end}}'
kubectl get pods -o custom-columns=NAME:.metadata.name
kubectl get pods -o=jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.status.podIP}{"\n"}{end}'
```

#### Port forward
```
kubectl port-forward pod/k8-nginx 8080:80
http://localhost:8080/
```

#### Proxy
```
# start porxy
kubectl proxy

# proxy to pod
http://localhost:8001/api/v1/namespaces/default/pods/k8-nginx-59ff48f654-ww24h/proxy/
```

#### Logs
```
kubectl logs pod/k8-nginx
kubectl logs -f pod/k8-nginx
```

#### Exec
```
kubectl exec <pod-name> <command>
kubectl exec k8-nginx date
kubectl exec k8-nginx -it bash
```

#### Copy
```
kubectl cp <pod-name>:/captures/capture3.txt ./capture3.txt
kubectl cp $HOME/config.txt <pod-name>:/config.txt
```

### Deployment
```
kubectl run nginx --generator=deployment/v1beta1 --image=nginx:1.16 --labels=app=frontend,env=prod --replicas=5 --dry-run=true -o yaml

# new way
kubectl create deployment nginx-test --image=nginx:1.16 -o yaml --dry-run=true
kubectl scale --replicas=5 deployment.apps/nginx
```
### Service
```
kubectl create service clusterip redis --tcp=6379:6379 --dry-run -o yaml
kubectl create service nodeport nginx --tcp=80:80 --node-port=30080 --dry-run -o yaml
```

### Namespace
```
kubectl get namespaces
kubectl get pods --all-namespaces
kubectl get pods --namespace=default

# get current context
kubectl config current-context

# switch to namespace
kubectl config set-context $(kubectl config current-context) --namespace=kube-system
# start using context
kubectl config use-context kube-system
```

### Labels & Annotations
```
# show labels
kubectl get pods --show-labels
kubectl get pods -L app

# all pods with app label set
kubectl get pods --selector="app"

# all pods with label app set to specific value
kubectl get pods --selector="app=k8-nginx"
kubectl get pods --selector="app!=k8-nginx"

# all pods with multiple labels
kubectl get pods --selector="app=k8-nginx,color=blue"
kubectl get pods --selector="color in (red, blue)"
kubectl get pods --selector="color notin (red, blue)"

# create new label
kubectl label pods k8-nginx color=red

# update annotation
kubectl annotate pods k8-nginx version=v2 --overwrite

# remove label
kubectl label pods k8-nginx color-
```