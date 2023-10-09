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
