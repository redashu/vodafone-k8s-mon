## Introduction to prometheus 

<img src="prm1.png">

### prometheus with k8s 

<img src="prom2.png">

### Prometheus component 

<img src="prom3.png">

### docker-compose yaml file for prometheus server

```
version: '3'
services:
  prometheus:
    image: prom/prometheus # official docker image of promethes -- this image will be used by k8s 
    container_name: ashu-prometheus # name of container 
    ports:
      - "1234:9090"  # docker port forwarding  hostport(1024-60000):containerport (9090)
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
      - prometheus-ashudata:/prometheus # location of TSDB 
volumes:
  prometheus-ashudata:

```

### lets deploy it 

```
[ec2-user@vodafone ashu-monitoring]$ ls
docker-compose.yaml  prometheus.yml
[ec2-user@vodafone ashu-monitoring]$ vim docker-compose.yaml 
[ec2-user@vodafone ashu-monitoring]$ vim prometheus.yml 
[ec2-user@vodafone ashu-monitoring]$ ls
docker-compose.yaml  prometheus.yml
[ec2-user@vodafone ashu-monitoring]$ docker-compose  up -d 
[+] Running 3/3
 ✔ Network ashu-monitoring_default               Created                                                                   0.0s 
 ✔ Volume "ashu-monitoring_prometheus-ashudata"  Created                                                                   0.0s 
 ✔ Container ashu-prometheus                     Started                                                                   0.0s 
[ec2-user@vodafone ashu-monitoring]$ docker-compose  ps
NAME                IMAGE               COMMAND                  SERVICE             CREATED             STATUS              PORTS
ashu-prometheus     prom/prometheus     "/bin/prometheus --c…"   prometheus          5 seconds ago       Up 4 seconds        0.0.0.0:1234->9090/tcp, :::1234->9090/tcp
[ec2-user@vodafone ashu-monitoring]$ 



```

### Understanding role of exporters to retric metrics from app /server -- pull those metrics by promethes (retrival)

<img src="exp.png">

### Exportes in prometheus

<img src="exp2.png">

### adding node export in docker-compose 

```
version: '3'
services:
  node-exporter:
    image: prom/node-exporter
    container_name: ashunode1-exporter
    ports:
      - "1199:9100" # default port is 9100 i am taking 1199 as my host port 
  prometheus:
    image: prom/prometheus # official docker image of promethes -- this image will be used by k8s 
    container_name: ashu-prometheus # name of container 
    ports:
      - "1234:9090"  # docker port forwarding  hostport(1024-60000):containerport (9090)
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
      - prometheus-ashudata:/prometheus # location of TSDB 
volumes:
  prometheus-ashudata:

```

### also adding in prometheus.yml

```
global:
  scrape_interval: 15s
scrape_configs:
  - job_name: 'prometheus'
    static_configs:
      - targets: ['43.204.182.9:1234']

  - job_name:  'ashutoshh-node-exporter'
    static_configs:
      - targets: ['43.204.182.9:1199']

```

### lets redeploy it 

```
 docker-compose  down
[ec2-user@vodafone ashu-monitoring]$ docker-compose  up -d
[+] Running 3/3
 ✔ Network ashu-monitoring_default  Created                                                                                                                  0.0s 
 ✔ Container ashu-prometheus        Started                                                                                                                  0.0s 
 ✔ Container ashunode1-exporter     Started                                                                                                                  0.0s 
[ec2-user@vodafone ashu-monitoring]$ docker-compose  ps
NAME                 IMAGE                COMMAND                  SERVICE             CREATED             STATUS              PORTS
ashu-prometheus      prom/prometheus      "/bin/prometheus --c…"   prometheus          7 seconds ago       Up 6 seconds        0.0.0.0:1234->9090/tcp, :::1234->9090/tcp
ashunode1-exporter   prom/node-exporter   "/bin/node_exporter"     node-exporter       7 seconds ago       Up 6 seconds        0.0.0.0:1199->9100/tcp, :::1199->9100/tcp
[ec2-user@vodafone ashu-monitoring]$ docker-compose  down 
```


## getting started with PromQL 

### data type in PromQL 

<img src="type1.png">

### range vector instance

<img src="type2.png">

## Creating custom namespace in k8s 

```
[ec2-user@vodafone ~]$ kubectl  create  ns ashu-project 
namespace/ashu-project created
[ec2-user@vodafone ~]$ 
[ec2-user@vodafone ~]$ kubectl   get  ns
NAME              STATUS   AGE
ashu-project      Active   4s
default           Active   6h42m
kube-node-lease   Active   6h42m
kube-public       Active   6h42m
kube-system       Active   6h42m
[ec2-user@vodafone ~]$ 
[ec2-user@vodafone ~]$ kubectl config set-context --current --namespace ashu-project 
Context "arn:aws:eks:ap-south-1:751136288263:cluster/vodafone-eks" modified.
[ec2-user@vodafone ~]$ 
[ec2-user@vodafone ~]$ kubectl   config get-contexts 
CURRENT   NAME                                                       CLUSTER                                                    AUTHINFO                                                   NAMESPACE
*         arn:aws:eks:ap-south-1:751136288263:cluster/vodafone-eks   arn:aws:eks:ap-south-1:751136288263:cluster/vodafone-eks   arn:aws:eks:ap-south-1:751136288263:cluster/vodafone-eks   ashu-project
[ec2-user@vodafone ~]$ 
[ec2-user@vodafone ~]$ kubectl  get pod
No resources found in ashu-project namespace.
[ec2-user@vodafone ~]$ 

```

### Installing prometheus-kube on EKS using helm 

```
helm repo list
NAME           	URL                                               
ashu-prometheus	https://prometheus-community.github.io/helm-charts
```

### installing -- 

```
[ec2-user@vodafone ~]$ helm install ashu-prom ashu-prometheus/kube-prometheus-stack --version 51.4.0
NAME: ashu-prom
LAST DEPLOYED: Mon Oct  9 10:00:41 2023
NAMESPACE: ashu-project
STATUS: deployed
REVISION: 1
NOTES:
kube-prometheus-stack has been installed. Check its status by running:
  kubectl --namespace ashu-project get pods -l "release=ashu-prom"

Visit https://github.com/prometheus-operator/kube-prometheus for instructions on how to create & configure Alertmanager and Prometheus instances using the Operator.
[ec2-user@vodafone ~]$ 
[ec2-user@vodafone ~]$ 

```

### verify that 

```
[ec2-user@vodafone ~]$ helm ls
NAME     	NAMESPACE   	REVISION	UPDATED                                	STATUS  	CHART                       	APP VERSION
ashu-prom	ashu-project	1       	2023-10-09 10:05:38.218362561 +0000 UTC	deployed	kube-prometheus-stack-51.4.0	v0.68.0    
[ec2-user@vodafone ~]$ 
[ec2-user@vodafone ~]$ 
[ec2-user@vodafone ~]$ kubectl  get  po 
NAME                                                    READY   STATUS    RESTARTS   AGE
alertmanager-ashu-prom-kube-prometheus-alertmanager-0   2/2     Running   0          43s
ashu-prom-grafana-6f55c8579-272j5                       3/3     Running   0          46s
ashu-prom-kube-prometheus-operator-85696fc8b9-79h6m     1/1     Running   0          46s
ashu-prom-kube-state-metrics-f976cfb9d-sgcbh            1/1     Running   0          46s
ashu-prom-prometheus-node-exporter-8h9gg                1/1     Running   0          46s
ashu-prom-prometheus-node-exporter-fkmrv                1/1     Running   0          46s
ashu-prom-prometheus-node-exporter-qznlv                1/1     Running   0          46s
ashu-prom-prometheus-node-exporter-rpx55                1/1     Running   0          46s
prometheus-ashu-prom-kube-prometheus-prometheus-0       2/2     Running   0          43s
[ec2-user@vodafone ~]$ kubectl  get svc
NAME                                     TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
alertmanager-operated                    ClusterIP   None            <none>        9093/TCP,9094/TCP,9094/UDP   46s
ashu-prom-grafana                        ClusterIP   10.100.53.153   <none>        80/TCP                       49s
ashu-prom-kube-prometheus-alertmanager   ClusterIP   10.100.117.82   <none>        9093/TCP,8080/TCP            49s
ashu-prom-kube-prometheus-operator       ClusterIP   10.100.22.28    <none>        443/TCP                      49s
ashu-prom-kube-prometheus-prometheus     ClusterIP   10.100.78.158   <none>        9090/TCP,8080/TCP            49s
ashu-prom-kube-state-metrics             ClusterIP   10.100.31.87    <none>        8080/TCP                     49s
ashu-prom-prometheus-node-exporter       ClusterIP   10.100.23.157   <none>        9100/TCP                     49s
prometheus-operated                      ClusterIP   None            <none>        9090/TCP                     46s
[ec2-user@vodafone ~]$ 


```

###  pulling chart to understand it 

```
[ec2-user@vodafone ~]$ helm repo ls
NAME           	URL                                               
ashu-prometheus	https://prometheus-community.github.io/helm-charts
[ec2-user@vodafone ~]$ 
[ec2-user@vodafone ~]$ helm pull  ashu-prometheus/kube-prometheus-stack --version 51.4.0 
[ec2-user@vodafone ~]$ 
[ec2-user@vodafone ~]$ ls
ashu-monitoring  kube-prometheus-stack-51.4.0.tgz  poc
[ec2-user@vodafone ~]$ 
[ec2-user@vodafone ~]$ helm pull  ashu-prometheus/kube-prometheus-stack --version 51.4.0  --untar 
[ec2-user@vodafone ~]$ 
[ec2-user@vodafone ~]$ ls
ashu-monitoring  kube-prometheus-stack  kube-prometheus-stack-51.4.0.tgz  poc
[ec2-user@vodafone ~]$ 

```

### helm chart important things 

```
ec2-user@vodafone ~]$ cd  kube-prometheus-stack/
[ec2-user@vodafone kube-prometheus-stack]$ ls
CONTRIBUTING.md  Chart.lock  Chart.yaml  README.md  charts  templates  values.yaml
[ec2-user@vodafone kube-prometheus-stack]$ 

```

### understanding it more 

<img src="um.png">

## Database metric monitoring using Prometheus 

### mysql database deployment 

```
[ec2-user@vodafone ashu-mysql-poc]$ kubectl  create deployment ashu-mysqldb --image=mysql --port 3306  --dry-run=client -o yaml   
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: ashu-mysqldb
  name: ashu-mysqldb
spec:
  replicas: 1
  selector:
    matchLabels:
      app: ashu-mysqldb
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: ashu-mysqldb
    spec:
      containers:
      - image: mysql
        name: mysql
        ports:
        - containerPort: 3306
        resources: {}
status: {}
[ec2-user@vodafone ashu-mysql-poc]$ kubectl  create deployment ashu-mysqldb --image=mysql --port 3306  --dry-run=client -o yaml     >deploy.yaml 

```

### creating secret for mysql db deployment 

```
kubectl  create secret  generic ashu-db-cred --from-literal MYSQL_ROOT_PASSWORD=Dbhello@12345   --from-literal MYSQL_USER=ashu  --from-literal  MYSQL_PASSWORD=Roj@0987  --dry-run=client -o yaml
```

### final secret creation yaml 

```
kubectl  create secret  generic ashu-db-cred --from-literal MYSQL_ROOT_PASSWORD=Dbhello@12345   --from-literal MYSQL_USER=ashu  --from-literal  MYSQL_PASSWORD=Roj@0987  --dry-run=client -o yaml >secret.yaml 
[ec2-user@vodafone ashu-mysql-poc]$ ls
dbcred.txt  deploy.yaml  secret.yaml  service.yaml
```

### final database manifest file 

```
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: ashu-mysqldb
  name: ashu-mysqldb
spec:
  replicas: 1
  selector:
    matchLabels:
      app: ashu-mysqldb
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: ashu-mysqldb
    spec:
      containers:
      - image: mysql
        name: mysql
        ports:
        - containerPort: 3306
        resources: {}
        env: # to put envirment variables
        - name: MYSQL_DATABASE
          value: ashu-emp-db
        envFrom:
        - secretRef:
            name: ashu-db-cred
status: {}

```

### creating database 

```
[ec2-user@vodafone ashu-mysql-poc]$ ls
dbcred.txt  deploy.yaml  secret.yaml  service.yaml
[ec2-user@vodafone ashu-mysql-poc]$ 
[ec2-user@vodafone ashu-mysql-poc]$ 
[ec2-user@vodafone ashu-mysql-poc]$ kubectl  apply -f deploy.yaml  -f secret.yaml  
deployment.apps/ashu-mysqldb created
secret/ashu-db-cred created

```

### creating service for db access

```
[ec2-user@vodafone ashu-mysql-poc]$ kubectl  get deploy
NAME           READY   UP-TO-DATE   AVAILABLE   AGE
ashu-mysqldb   1/1     1            1           7m5s
[ec2-user@vodafone ashu-mysql-poc]$ kubectl  expose deployment  ashu-mysqldb  --type ClusterIP --port 3306 --name ashu-db-svc    --dry-run=client -o yaml  
apiVersion: v1
kind: Service
metadata:
  creationTimestamp: null
  labels:
    app: ashu-mysqldb
  name: ashu-db-svc
spec:
  ports:
  - port: 3306
    protocol: TCP
    targetPort: 3306
  selector:
    app: ashu-mysqldb
  type: ClusterIP
status:
  loadBalancer: {}
[ec2-user@vodafone ashu-mysql-poc]$ kubectl  expose deployment  ashu-mysqldb  --type ClusterIP --port 3306 --name ashu-db-svc    --dry-run=client -o yaml  >dbsvc.yaml 
[ec2-user@vodafone ashu-mysql-poc]$ kubectl  apply -f dbsvc.yaml 
service/ashu-db-svc created
[ec2-user@vodafone ashu-mysql-poc]$ kubectl  get svc
NAME          TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
ashu-db-svc   ClusterIP   10.100.142.27   <none>        3306/TCP   5s
[ec2-user@vodafone ashu-mysql-poc]$ 

```

### Now creating mysql-exporter 

```
kubectl   create  deployment ashu-mysql-exporter --image=prom/mysqld-exporter --port 9104  --dry-run=client -o yaml  
```
### Creating configmap for mysql-exporter to connect any DB of mysql instance

### my.cnf 

```
[client]
user=root
password=Dbhello@12345
port=3306
host=ashu-db-svc
```

```
[ec2-user@vodafone mysql-exporter]$ ls
my.cnf  mysql_exporter.yaml
[ec2-user@vodafone mysql-exporter]$ 
[ec2-user@vodafone mysql-exporter]$ kubectl  create configmap  ashu-db-cm  --from-file=my.cnf --dry-run=client -o yaml 
apiVersion: v1
data:
  my.cnf: |
    [client]
    user=root
    password=Dbhello@12345
    port=3306
    host=ashu-db-svc
kind: ConfigMap
metadata:
  creationTimestamp: null
  name: ashu-db-cm
[ec2-user@vodafone mysql-exporter]$ kubectl  create configmap  ashu-db-cm  --from-file=my.cnf --dry-run=client -o yaml >cm.yaml
[ec2-user@vodafone mysql-exporter]$ ls
cm.yaml  my.cnf  mysql_exporter.yaml
[ec2-user@vodafone mysql-exporter]$ kubectl  create -f cm.yaml 
configmap/ashu-db-cm created
[ec2-user@vodafone mysql-exporter]$ kubectl  get  cm
NAME               DATA   AGE
ashu-db-cm         1      4s
kube-root-ca.crt   1      167m
[ec2-user@vodafone mysql-exporter]$ 
```
### volume updated on mysql-export.yaml

```
[ec2-user@vodafone mysql-exporter]$ cat mysql_exporter.yaml 
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: ashu-mysql-exporter
  name: ashu-mysql-exporter
spec:
  replicas: 1
  selector:
    matchLabels:
      app: ashu-mysql-exporter
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: ashu-mysql-exporter
    spec:
      volumes:
      - name: ashu-exp-vol
        configMap:
          name: ashu-db-cm 
      containers:
      - image: prom/mysqld-exporter
        name: mysqld-exporter
        ports:
        - containerPort: 9104
        resources: {}
        volumeMounts:
          - name: ashu-exp-vol
            mountPath: /etc/my.cnf 
            readOnly: true 
status: {}

```

### final update in mysql-exporter.yaml

```
[ec2-user@vodafone mysql-exporter]$ cat mysql_exporter.yaml 
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: ashu-mysql-exporter
  name: ashu-mysql-exporter
spec:
  replicas: 1
  selector:
    matchLabels:
      app: ashu-mysql-exporter
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: ashu-mysql-exporter
    spec:
      volumes:
      - name: ashu-exp-vol
        configMap:
          name: ashu-db-cm 
      containers:
      - image: prom/mysqld-exporter
        name: mysqld-exporter
        args: # to pass cmd argument in container 
          - "--collect.auto_increment.columns"
          - "--config.my-cnf=/etc/my.cnf"  
          - "--web.listen-address=:9104"  
          - "--web.telemetry-path=/metrics"
        ports:
        - containerPort: 9104
        resources: {}
        volumeMounts:
          - name: ashu-exp-vol
            mountPath: /etc/my.cnf 
            subPath: my.cnf
            readOnly: true 
status: {}

```

### lets deploy it 

```
[ec2-user@vodafone mysql-exporter]$ kubectl  apply -f mysql_exporter.yaml 
deployment.apps/ashu-mysql-exporter configured
[ec2-user@vodafone mysql-exporter]$ 
[ec2-user@vodafone mysql-exporter]$ 
[ec2-user@vodafone mysql-exporter]$ kubectl  get deploy
NAME                  READY   UP-TO-DATE   AVAILABLE   AGE
ashu-mysql-exporter   1/1     1            1           113s
ashu-mysqldb          1/1     1            1           64m
[ec2-user@vodafone mysql-exporter]$ kubectl  get cm
NAME               DATA   AGE
ashu-db-cm         1      20m
kube-root-ca.crt   1      3h7m
[ec2-user@vodafone mysql-exporter]$ kubectl  get po
NAME                                  READY   STATUS    RESTARTS   AGE
ashu-mysql-exporter-75989f9f9-vsnsg   1/1     Running   0          2m1s
ashu-mysqldb-5bf89fc7f5-z2q7p         1/1     Running   0          64m
[ec2-user@vodafone mysql-exporter]$ 

```


