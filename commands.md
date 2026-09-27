#### create Minikube cluster
    minikube start --cpus 4 --memory 8192 --vm-driver hyperkit

#### install Prometheus-operator
###### add repos
    helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
    helm repo add stable https://kubernetes-charts.storage.googleapis.com/
    helm repo update

###### install chart
    helm install prometheus prometheus-community/kube-prometheus-stack

###### install chart with fixed version    
    helm install prometheus prometheus-community/kube-prometheus-stack --version "9.4.1"

###### Link to chart
[https://github.com/prometheus-community/helm-charts/tree/main/charts/kube-prometheus-stack]




#### install Mongodb-exporter
###### add repos
    helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
    helm repo update

###### install chart
    helm install mongodb-exporter prometheus-community/prometheus-mongodb-exporter

###### install chart with fixed version    
    helm install mongodb-exporter prometheus-community/prometheus-mongodb-exporter --version "2.8.1"

###### Link to chart
[https://github.com/prometheus-community/helm-charts/tree/main/charts/prometheus-mongodb-exporter]




#### port-forwardings
###### Prometheus-UI
    kubectl port-forward svc/prometheus-kube-prometheus-prometheus 9090:9090 --address=0.0.0.0

###### Alert Manager UI
    kubectl port-forward svc/prometheus-kube-prometheus-alertmanager 9093:9093 --address=0.0.0.0

###### Grafana
    kubectl port-forward deployment/prometheus-grafana 3000:3000  --address=0.0.0.0

###### Grafana Dashboard credentials
    user: admin
    pwd: kubectl get secret prometheus-grafana \
  -o jsonpath="{.data.admin-password}" | base64 --decode

###### Mongodb-exporter 
    kubectl port-forward service/mongodb-exporter-prometheus-mongodb-exporter 9216  

