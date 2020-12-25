# Patroni and Kubernetes with microk18s
## Incomplete..

Referencing from https://juan-medina.com/2019/12/12/postgresql-k8s/

https://postgres-operator.readthedocs.io/en/latest/quickstart/


```
git clone git@github.com:zalando/postgres-operator.git

cd postgres-operator

 # configuration
 
sudo microk8s kubectl create -f  manifests/configmap.yaml

sudo microk8s kubectl create -f manifests/operator-service-account-rbac.yaml
sudo microk8s kubectl create -f manifests/postgres-operator.yaml 
# operator API to be used by UI
microk8s kubectl create -f manifests/api-service.yaml

 

microk8s kubectl delete manifests/configmap.yaml
microk8s kubectl delete -f manifests/operator-service-account-rbac.yaml
microk8s kubectl delete -f manifests/postgres-operator.yaml
 
```


## Check if postgres-opearator running

# if you've created the operator using yaml manifests

microk8s kubectl get pod -l name=postgres-operator

# if you've created the operator using helm chart

microk8s kubectl get pod -l app.kubernetes.io/name=postgres-operator


microk8s kubectl logs "$(kubectl get pod -l name=postgres-operator --output='name')"


## Deploy Operator UI

## not working
microk8s kubectl apply -f ui/manifests/

## seems working

microk8s kubectl apply -k github.com/zalando/postgres-operator/ui/manifests


# check UI pod running

# if you've created the operator using yaml manifests

microk8s kubectl get pod -l name=postgres-operator-ui
 

# Port Forward

 microk8s kubectl port-forward svc/postgres-operator-ui 8081:80

# check if running there


http://localhost:8081/


----


## Create a Postgres cluster


microk8s kubectl create -f manifests/minimal-postgres-manifest.yaml

Check here

http://localhost:8081/#/list


# check the deployed cluster
microk8s kubectl get postgresql

# check created database pods
microk8s kubectl get pods -l application=spilo -L spilo-role

# check created service resources
microk8s  kubectl get svc -l application=spilo -L spilo-role


# Connect to postgresql 

export HOST_PORT=$(minikube service acid-minimal-cluster --url | sed 's,.*/,,')
export PGHOST=$(echo $HOST_PORT | cut -d: -f 1)
export PGPORT=$(echo $HOST_PORT | cut -d: -f 2)


microk8s kubectl describe service/acid-minimal-cluster 

Endpoints:         10.1.69.89:5432

export PGHOST=10.1.69.89
export PGPORT=5432


microk8s kubectl get secret postgres.acid-minimal-cluster.credentials -o 'jsonpath={.data.password}' | base64 -d



export PGPASSWORD=$(microk8s kubectl get secret postgres.acid-minimal-cluster.credentials -o 'jsonpath={.data.password}' | base64 -d)
export PGSSLMODE=require
psql -U postgres

--------



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
