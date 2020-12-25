sudo snap remove microk8s 

sudo snap install microk8s --classic

microk8s status --wait-ready

snap enable microk8s

microk8s enable dashboard dns registry istio

microk8s kubectl get all --all-namespaces


#### Access Dashboard

microk8s dashboard-proxy

### Start and Stop

microk8s start 


microk8s stop


## To reset

microk8s reset --destroy-storage


## Dashboard


https://microk8s.io/docs/addon-dashboard


microk8s enable dashboard

token=$(microk8s kubectl -n kube-system get secret | grep default-token | cut -d " " -f1)


echo $token

microk8s kubectl -n kube-system describe secret $token

microk8s kubectl port-forward -n kube-system service/kubernetes-dashboard 10443:443

check 

 https://127.0.0.1:10443
