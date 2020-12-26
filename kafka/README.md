Based on 
https://medium.com/@prasanta.mohanty/deploy-kafka-cluster-on-microk8s-in-15-mins-f3d5081991e8

microk8s enable helm

# not working
microk8s helm init

# working

microk8s helm init --stable-repo-url https://charts.helm.sh/stable --service-account tiller

Non-java generic commands

sudo apt install kafkacat

mkdir kafka-setup
cd kafka-setup

git clone https://github.com/confluentinc/cp-helm-charts.git

#we do not want to do the changes on repo 

cp cp-helm-charts/values.yaml . 

microk8s helm install --name my-confluent cp-helm-charts -f ./values.yaml

Got error as could not find tiller

microk8s kubectl get all --all-namespaces | grep tiller
microk8s kubectl delete deployment tiller-deploy -n kube-system
microk8s kubectl delete service tiller-deploy -n kube-system
microk8s kubectl get all --all-namespaces | grep tiller


