## Revision 

<img src="rev.png">

### checking connection for eks 

```
[ec2-user@vodafone ~]$ kubectl   get nodes
NAME                                            STATUS   ROLES    AGE   VERSION
ip-192-168-48-24.ap-south-1.compute.internal    Ready    <none>   18m   v1.25.13-eks-43840fb
ip-192-168-5-125.ap-south-1.compute.internal    Ready    <none>   18m   v1.25.13-eks-43840fb
ip-192-168-5-73.ap-south-1.compute.internal     Ready    <none>   20h   v1.25.13-eks-43840fb
ip-192-168-60-140.ap-south-1.compute.internal   Ready    <none>   12h   v1.25.13-eks-43840fb
[ec2-user@vodafone ~]$ 
[ec2-user@vodafone ~]$ kubectl  config get-contexts 
CURRENT   NAME                                                       CLUSTER                                                    AUTHINFO                                                   NAMESPACE
*         arn:aws:eks:ap-south-1:751136288263:cluster/vodafone-eks   arn:aws:eks:ap-south-1:751136288263:cluster/vodafone-eks   arn:aws:eks:ap-south-1:751136288263:cluster/vodafone-eks   ashu-project
[ec2-user@vodafone ~]$ 




```

### promethesu and grafana with Ingress controller 

```
[ec2-user@vodafone ~]$ kubectl  get all -n nginx-ingress-sample
[ec2-user@vodafone ~]$ kubectl  get all -n nginx-ingress-sample
NAME                                                     READY   STATUS    RESTARTS   AGE
pod/my-nginx-ingress-nginx-controller-8649966447-ts8gs   1/1     Running   0          12h

NAME                                                  TYPE           CLUSTER-IP      EXTERNAL-IP                                                                PORT(S)                      AGE
service/my-nginx-ingress-nginx-controller             LoadBalancer   10.100.12.252   a007dc21a36fd4131b54a0f746b3c6a7-1932653871.ap-south-1.elb.amazonaws.com   80:30831/TCP,443:30032/TCP   12h
service/my-nginx-ingress-nginx-controller-admission   ClusterIP      10.100.62.187   <none>                                                                     443/TCP                      12h
service/my-nginx-ingress-nginx-controller-metrics     ClusterIP      10.100.208.69   <none>                                                                     10254/TCP                    12h

NAME                                                READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/my-nginx-ingress-nginx-controller   1/1     1            1           12h

NAME                                                           DESIRED   CURRENT   READY   AGE
replicaset.apps/my-nginx-ingress-nginx-controller-8649966447   1         1         1       12h
[ec2-user@vodafone ~]$ 
[ec2-user@vodafone ~]$ 
[ec2-user@vodafone ~]$ kubectl  get  ingress -n monitoring 
NAME              CLASS   HOSTS                  ADDRESS                                                                    PORTS   AGE
grafana-ingress   nginx   grafana.delvex.io      a007dc21a36fd4131b54a0f746b3c6a7-1932653871.ap-south-1.elb.amazonaws.com   80      12h
minimal-ingress   nginx   prometheus.delvex.io   a007dc21a36fd4131b54a0f746b3c6a7-1932653871.ap-south-1.elb.amazonaws.com   80      12h
[ec2-user@vodafone ~]$ kubectl  get  ingress grafana-ingress  -n monitoring -o yaml 
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
  creationTimestamp: "2023-10-09T16:46:50Z"
  generation: 1
  name: grafana-ingress
  namespace: monitoring
  resourceVersion: "313375"
  uid: fd249c08-0e9c-4291-b6ef-f7ede9ecc184
spec:
  ingressClassName: nginx
  rules:
  - host: grafana.delvex.io
    http:
      paths:
      - backend:
          service:
            name: my-kube-prometheus-stack-grafana
            port:
              number: 80
        path: /
        pathType: Prefix
status:
  loadBalancer:
    ingress:
    - hostname: a007dc21a36fd4131b54a0f746b3c6a7-1932653871.ap-south-1.elb.amazonaws.com
```
