### apply db manifest

```
[ec2-user@vodafone mongo-db-webapp]$ ls
mongo-cm.yaml  mongo-sts.yaml  mongo-svc.yaml
[ec2-user@vodafone mongo-db-webapp]$ 
[ec2-user@vodafone mongo-db-webapp]$ kubectl apply -f .
configmap/mongo-init-config unchanged
statefulset.apps/mongodb unchanged
service/mongodb unchanged
[ec2-user@vodafone mongo-db-webapp]$ 
[ec2-user@vodafone mongo-db-webapp]$ kubectl  get po 
NAME        READY   STATUS    RESTARTS   AGE
mongodb-0   1/1     Running   0          4m13s
[ec2-user@vodafone mongo-db-webapp]$ kubectl  get cm
NAME                DATA   AGE
ashu-db-cm          1      41h
kube-root-ca.crt    1      44h
mongo-init-config   1      9m18s
[ec2-user@vodafone mongo-db-webapp]$ kubectl  get svc
NAME      TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)     AGE
mongodb   ClusterIP   10.100.91.118   <none>        27017/TCP   94s
[ec2-user@vodafone mongo-db-webapp]$ kubectl  get ep
NAME      ENDPOINTS             AGE
mongodb   192.168.30.22:27017   97s
[ec2-user@vodafone mongo-db-webapp]$ 

```

### Deploy web part 

```
[ec2-user@vodafone mongo-db-webapp]$ kubectl  apply -f ashu-flask-cm.yaml  -f ashu-flask-deploy.yaml  -f ashu-flask-svc.yaml 
configmap/nginx-config unchanged
deployment.apps/dashboard unchanged
service/dashboard unchanged
[ec2-user@vodafone mongo-db-webapp]$ 
[ec2-user@vodafone mongo-db-webapp]$ kubectl  get po,cm,svc
NAME                             READY   STATUS    RESTARTS   AGE
pod/dashboard-6c7f499c8f-6nl75   2/2     Running   0          116s
pod/mongodb-0                    1/1     Running   0          14m

NAME                          DATA   AGE
configmap/ashu-db-cm          1      41h
configmap/kube-root-ca.crt    1      44h
configmap/mongo-init-config   1      19m
configmap/nginx-config        1      6m34s

NAME                TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)     AGE
service/dashboard   ClusterIP   10.100.81.9     <none>        80/TCP      3m13s
service/mongodb     ClusterIP   10.100.91.118   <none>    
```
