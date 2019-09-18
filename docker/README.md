# Kubernetes the hard way using Docker
This example uses Docker containers to create a K8s cluster with 3 masters (stacked) nodes and 2 worker nodes.

## Starting cluster
```
docker network create --driver=bridge --subnet=172.27.0.0/16 --gateway=172.27.0.1 k8s_net
docker-compose up -d
```

### Stoping cluster
```
docker-compose stop
```