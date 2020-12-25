# Patroni and Kubernetes with microk18s
## Incomplete..

Referencing from https://juan-medina.com/2019/12/12/postgresql-k8s/

```
git clone git@github.com:zalando/postgres-operator.git

cd postgres-operator

sudo microk8s kubectl create -f  manifests/configmap.yaml
sudo microk8s kubectl create -f manifests/operator-service-account-rbac.yaml
sudo microk8s kubectl create -f manifests/postgres-operator.yaml 

microk8s kubectl delete manifests/configmap.yaml
microk8s kubectl delete -f manifests/operator-service-account-rbac.yaml
microk8s kubectl delete -f manifests/postgres-operator.yaml
 
```


create file movies-db.yaml

nano movies-db.yaml

paste below content

```

apiVersion: "acid.zalan.do/v1"
kind: postgresql
metadata:
  name: movies-db-cluster
  namespace: default
spec:
  teamId: "movies"
  volume:
    size: 1Gi
  numberOfInstances: 2
  users:
    moviesdba:  # database owner
    - superuser
    - createdb
    moviesuser: []  # roles
  databases:
    movies: moviesdba  # dbname: owner
  postgresql:
    version: "11"
    
```    

```
sudo microk8s kubectl create -f movies-db.yaml 

sudo microk8s kubectl create -f manifests/movies-db.yaml 

sudo microk8s kubectl get postgresql

```
