# Prometheus_grafana_kubernetes_EFS_persistent_volume

ref --> https://github.com/baskarvj/persistent_volume_kubernetes for persistent volume creation

Note: need to create seperate pvc for both prometheus and garfana, it will use the same efs and it creates different access points
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

Note: need to create seperate pvc for both prometheus and garfana, it will use the same efs and it creates different access points

~~~
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
~~~

### for EBS volume
~~~
vi grafana.yml
~~~
~~~
service:
  type: NodePort
persistence:
  type: pvc
  enabled: true
  storageClassName: ebs-sc
    #  existingClaim: efs-claim
  accessModes:
    - ReadWriteOnce
  size: 10Gi

plugins:
- alexanderzobnin-zabbix-app
- grafana-clock-panel

datasources:
 datasources.yaml:
   apiVersion: 1
   datasources:
   - name: Prometheus
     type: prometheus
     url: http://prometheus-server
     access: proxy
     isDefault: true

adminUser: admin
adminPassword: <Enter_your_password_here>


~~~

### for EFS volume   Note: will get database is locked error but it will work
~~~
vi grafana.yml
~~~
~~~
service:
  type: NodePort
persistence:
  type: pvc
  enabled: true
  existingClaim: "<create_and_mention_existing_pvc>"
  accessModes:
    - ReadWriteMany
  size: 10Gi

initChownData:
  enabled: false

    #grafana.ini:
    #database:
    # wal: true

plugins:
- alexanderzobnin-zabbix-app
- grafana-clock-panel

datasources:
 datasources.yaml:
   apiVersion: 1
   datasources:
   - name: Prometheus
     type: prometheus
     url: http://prometheus-server
     access: proxy
     isDefault: true

adminUser: admin
adminPassword: <Enter_your_password_here>
~~~
~~~
helm install grafana grafana/grafana --values grafana.yml
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
