# Prometheus_grafana_kubernetes_EFS_persistent_volume

### Helm Installation ######
~~~
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh
~~~
~~~
helm version
~~~

### Prometheus Installation #######
~~~
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
~~~
~~~
vi prometheus.yml
~~~
~~~
server:
  persistentVolume:
    enabled: true
    existingClaim: "<create_and_mention_existing_pvc>"
  service:
    type: NodePort
alertmanager:
  enabled: false


~~~
~~~
helm install prometheus prometheus-community/prometheus --values prometheus.yml
~~~
~~~
kubectl get all
~~~
security group -- all tcp



### Grafana Installation #######
~~~
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
~~~
~~~
vi grafana.yml
~~~
~~~
service:
  type: NodePort

~~~
~~~
helm install grafana grafana/Grafana --values grafana
~~~
~~~
kubectl get all
~~~
## To get Admin password ##
~~~
kubectl get secret --namespace default grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
~~~

## Grafana Dashboard id ##

6417 -- pod, deployment, replicas
