# Certified Kubernetes Administrator (CKA)

* https://www.cncf.io/certification/cka/
* https://kodekloud.com/p/certified-kubernetes-administrator-with-practice-tests
* https://linuxacademy.com/linux/training/course/name/cloud-native-certified-kubernetes-administrator-cka

## Readiness Checklist
* Udemy Course
  * [ ] https://www.udemy.com/certified-kubernetes-administrator-with-practice-tests/
* Kubernetes Up and Running Book
  * [x] https://go.heptio.com/rs/383-ENX-437/images/Kubernetes_Up_and_Running.pdf?mkt_tok=eyJpIjoiTXpKalpHSTRabUk0WmpOaSIsInQiOiJqekdpSFc0WXRHYlpJMWdQeFpiOGtqXC8yUEo2VVwvV0JVYllMWjhYb1YyVEI3RTlNYnl5NnFyK3cwQ1YyK2J5Y2ZoQ1wvbU5wNmp3WWllWnI3eUNjRTFkK1N4NU52UHo5ZGRlZFJHRjIxTVwvZHVcL0Fab0RNR3Q1RjVxRWZHZG1ZYTNkIn0%3D
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

## Practice Tasks
1. Start pod and tie with service
```
kubectl run nginx --image=nginx:1.16 --labels="app=frontend,color=blue"
kubectl create service nodeport nginx --tcp=80:80 --node-port=30080
# update selector app=frontend
kubectl edit service nginx

# test
curl http://localhost:30080
```
2. Create clustor

## Weak Topics
* Static Pods
* Custom Scheduler

## Reference
### Generators
* https://kubernetes.io/docs/reference/kubectl/conventions/
* https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands

### Nodes
```
# get node names
kubectl get nodes --template '{{range .items}}{{.metadata.name}}{{"\n"}}{{end}}' | sort -u
kubectl get nodes -o jsonpath='{.items[*].metadata.name}'
```
```
# cordon
kubectl get nodes | awk '{print $1;}' | tail -20 | xargs kubectl cordon

# drain
kubectl drain --force --ignore-daemonsets --delete-local-data rd17d12ls-ztda09115201 --grace-period=-1 --timeout=1800s

# custom-columns
kubectl get pods -n default -o custom-columns=Node:.spec.nodeName | sort -u

# get master and worker nodes
kubectl get nodes -l node-role.kubernetes.io/master=true
kubectl get nodes -l node-role.kubernetes.io/node=true

# resource allocations
kubectl describe $(kubectl get node <nodename> -o name) | grep -A 6 Allocated

# get taints on nodes
kubectl get nodes -l proxytype=traefik -o=jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.spec.taints[*]}{"\n"}{end}'
```

### Pods
```
# deploy a pod
kubectl run nginx --generator=run-pod/v1 --image=nginx:1.16 --dry-run=true -o yaml
kubectl run --generator=run-pod/v1 redis --image=redis:alpine --labels=tier=db

# find pods with label bu=finance
kubectl get pods --selector bu=finance
kubectl get all --selector env=prod,bu=finance,tier=frontend

# show labels
kubectl get pods -o wide --show-labels

# without header
kubectl get pods -o wide --no-headers

# extract specific fields
kubectl get pods -o go-template --template '{{range .items}}{{.metadata.name}}{{"\n"}}{{end}}'
kubectl get pods -o custom-columns=NAME:.metadata.name
kubectl get pods -o=jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.status.podIP}{"\n"}{end}'
kubectl get pods -n weyland-k8s-service-pool-1 -o custom-columns=NODE:.spec.nodeName --no-headers | sort -u

# static pod
kubectl run --restart=Never --image=busybox static-busybox --dry-run -o yaml --command -- sleep 1000 > /etc/kubernetes/manifests/static-busybox.yaml
kubectl create -f /etc/kubernetes/manifests/static-busybox.yaml

kubectl get pods --all-namespaces --field-selector=spec.nodeName=mr36d01ls-geo06113201 --watch

# Get all pod resources on running on a specific node
# https://gist.github.com/so0k/42313dbb3b547a0f51a547bb968696ba

kubectl --kubeconfig pv50-kryptonite.config get pods -A --field-selector spec.nodeName=pv33d01ls-ztbu02011801 -w

kubectl --kubeconfig pv50-kryptonite.config get pods -n eval-neutron-pipeline-qa --field-selector spec.nodeName=pv33d01ls-ztbu02011801 -o json | jq -r '.items[] | .metadata.name + " \n Req. RAM: " + .spec.containers[].resources.requests.memory + " \n Lim. RAM: " + .spec.containers[].resources.limits.memory + " \n Req. CPU: " + .spec.containers[].resources.requests.cpu + " \n Lim. CPU: " + .spec.containers[].resources.limits.cpu + " \n Req. Eph. DISK: " + .spec.containers[].resources.requests["ephemeral-storage"] + " \n Lim. Eph. DISK: " + .spec.containers[].resources.limits["ephemeral-storage"] + "\n"'

# requested cpu on that node
kubectl --kubeconfig pv50-kryptonite.config get pods -A --field-selector spec.nodeName=pv33d01ls-ztbu02011801 -o json | jq -r '.items[] | "Req. CPU: " + .spec.containers[].resources.requests.cpu' | awk '{print $3}'
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

# interact with current cluster
kubectl config current-context
kube proxy
curl -k http://localhost:8001/version

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

# export with clustor information stripped
kubectl get deployment.apps/nginx --export -o yaml > nginx-deployment.yaml

# rollout
kubectl rollout history deployment.apps/nginx
kubectl rollout history deployment.apps/nginx --revision=1

kubectl rollout status deployment.apps/nginx

kubectl rollout pause deployment.apps/nginx
kubectl rollout resume deployment.apps/nginx

kubectl rollout undo deployment nginx
kubectl rollout undo deployment nginx --to-revision=1

# update image of running deployment
kubectl set image deployment/<deployment-name> <container-name>=<repository>/<image>:<tag> --record
kubectl set image deployment/web web=myprivateregistry.com:5000/nginx:alpine --record
kubectl rollout status -w deployment/<deployment-name>
```
### Service
```
kubectl create service clusterip redis --tcp=6379:6379 --dry-run -o yaml
kubectl create service nodeport nginx --tcp=80:80 --node-port=30080 --dry-run -o yaml

kubectl create service loadbalancer nginx --tcp:80:80
```

### Endpoints
```
kubectl get endpoints <service-name>
kubectl get endpoints nginx

kubectl describe endpoints nginx

# use scale replicas to watch new endpoints being added
kubectl get endpoints nginx --watch
```

### Namespace
```
kubectl get namespaces
kubectl get pods --all-namespaces
kubectl get pods --namespace=default

kubectl config view
kubectl config --kubeconfig=<config-file-path> use-context <context-name>

# get current context
kubectl config current-context

# switch to namespace
kubectl config set-context $(kubectl config current-context) --namespace=kube-system
# start using context
kubectl config use-context kube-system

kubectl api-resources --namespaced=false
kubectl api-resources --namespaced=true

# get ns quota
kubectl describe quota -n weyland-k8s-service-pool-1
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

### Metrics Server
```
git clone https://github.com/kubernetes-incubator/metrics-server.git
cd metrics-server/deploy/1.8+
kubectl apply -f .
kubectl top node
kubectl top pod
```

### ConfigMaps
```
kubectl create configmap my-config --from-literal=APP_COLOR=blue \
                                   --from-literal=APP_ENV=dev

kubectl get configmap
kubectl describe configmap my-config

kubectl create configmap my-file-config --from-file=config.properties
```

### Secrets
```
kubectl create secret generic db-secret --from-literal=DB_Host=sql01 --from-literal=DB_User=root --from-literal=DB_Password=password123

kubectl describe secret db-secret

# display base64 values 
kubectl get secret db-secret -o yaml
```

### RBAC
```
kubectl get roles
kubectl auth can-i create deployments
kubectl auth can-i delete nodes

kubectl auth can-i create deployments --as dev-user
kubectl auth can-i get pod --namespace=default --as dev-user

# check authorizaton scheme
kubectl describe pod/kube-apiserver-master  --namespace=kube-system | grep authorization

# get all roles
kubectl get roles --all-namespaces --no-headers | wc -l

# get details of a role
kubectl describe role/weave-net --namespace=kube-system

# get details of role binding
kubectl describe rolebinding/weave-net -n kube-system
```

### Cluster Roles
```
# get all cluster roles
kubectl get clusterroles --no-headers | wc -l

# get all cluster role bindings
kubectl get clusterrolebindings --no-headers | wc -l
```

### Events
```
kubectl get events --sort-by=.metadata.creationTimestamp | grep <search>
```

### ETCD
#### Commands
```
# user v3
export ETCDCTL_API=3

etcdctl put name john
etcdctl get name

# get all keys
etcdctl get / --prefix --keys-only
```
#### Quorum
N/2 + 1