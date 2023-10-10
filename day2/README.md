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

