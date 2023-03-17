---
title: k8s sevices
date: "2021-07-15T17:15:18+0800"
draft: false 
tags: ["k8s","ingress"]
---
[Understanding kubernetes networking: pods](https://medium.com/google-cloud/understanding-kubernetes-networking-pods-7117dd28727)

[Kubernetes Services simply visually explained](https://medium.com/swlh/kubernetes-services-simply-visually-explained-2d84e58d70e5)

[ Kubernetes Ingress simply visually explained](https://codeburst.io/kubernetes-ingress-simply-visually-explained-d9cad44e4419)

[Demystifying Kubernetes service discovery](https://nigelpoulton.com/demystifying-kubernetes-service-discovery/)

[浅谈Kubernetes Service负载均衡实现机制](https://xigang.github.io/2019/07/21/kubernetes-service/)

[Kubernetes| 找到容器不容易：Service、DNS 与服务发现](https://ld246.com/article/1591856369705)

1. create servie and servie types;
2. service implement  overivew
3. discuss service  and k8s
4. ingress


what:   提供稳定的ip and dns name 以方便访问容器 


types:
1. cluster ip
2. node port 
3.  外部:
	1. proxy
	2. load balancer 
	3. ingress



##  types 

cluster ip:    internal ip + port 
```
apiVersion: v1  
kind: Service  
metadata:  
	name: service-python  
spec:  
ports:  
	- port: 3000  
	  protocol: TCP  
	  targetPort: 443  
	selector:  
		run: pod-python  
	type: ClusterIP

```

![dEKWRO](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210716/dEKWRO.jpg)

nodePort: cluster ip +  node ip, port   
```yaml
apiVersion: v1  
kind: Service  
metadata:  
  name: service-python  
spec:  
  ports:  
  - port: 3000  
    protocol: TCP  
    targetPort: 443  
    nodePort: 30080
  selector:  
    run: pod-python  
	type: NodePort
```

![6ZSnjC](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210716/6ZSnjC.jpg)

load balancer: nodePort + external  ip 
![0YbEMG](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210716/0YbEMG.jpg)
![[Pasted image 20230210215248.png]]
```
apiVersion: v1  
kind: Service  
metadata:  
  name: service-python  
spec:  
  ports:  
  - port: 3000  
    protocol: TCP  
    targetPort: 443  
    nodePort: 30080  
  selector:  
    run: pod-python  
	type: LoadBalancer**
```



###  ingress controller 

what: 

a controller  to  implement gateway for   k8s


types:
1. nginx
2. kong
3. haproxy 

depoy a nginx ingress controller example:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-ingress
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx-ingress
  template:
    metadata:
      labels:
        app: nginx-ingress
    spec:
      serviceAccountName: nginx-ingress-serviceaccount
      containers:
        - name: nginx-ingress-controller
          image: mr.io/kubernetes-ingress-controller/nginx-ingress-controller:0.24.1
          args:
            - /nginx-ingress-controller
            - --configmap=$(POD_NAMESPACE)/nginx-configuration
            - --default-backend-service=$(POD_NAMESPACE)/default-http-backend
          env:
            - name: POD_NAME
              valueFrom:
                fieldRef:
                  fieldPath: metadata.name
            - name: POD_NAMESPACE
              valueFrom:
                fieldRef:
                  fieldPath: metadata.namespace
          ports:
            - name: http
              containerPort: 80
          livenessProbe:
            httpGet:
              path: /healthz
              port: 10254
              scheme: HTTP
            initialDelaySeconds: 10
            timeoutSeconds: 10
          readinessProbe:
            httpGet:
              path: /healthz
              port: 10254
              scheme: HTTP
            initialDelaySeconds: 10
            timeoutSeconds: 10
```




configure:  
```yaml
apiVersion: networking.k8s.io/v1beta1  
kind: Ingress  
metadata:  
  name: simple-fanout-example  
spec:  
  rules:  
  - host: api.myurl.com 
    http:  
      paths:  
      - path: /foo 
        backend:  
          serviceName: service1  
          servicePort: 4200  
      - path: /bar  
        backend:  
          serviceName: service2  
          servicePort: 8080  
  - host: website.myurl.com
    http:  
      paths:  
      - path: /
        backend:  
          serviceName: service3  
          servicePort: 3333

```




##  how

![OICxi4](https://raw.githubusercontent.com/atony2099/imgs/master/uPic/OICxi4.jpg)

![itoZdC](https://raw.githubusercontent.com/atony2099/imgs/master/uPic/itoZdC.jpg)

通过 kube-proxy实现路由；
通过 kube-dns 实现dns 解析: service name->cluster ip



![y8vLib](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20211206/y8vLib.jpg)
![8boFTl](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20211206/8boFTl.jpg)

1. kube-proxy 监听service，endpoint(pod);
2. set iptables or  ipvs 实现服务发现
    clusterIP-> iptables(ipvs)-> podIP

3.  dns-> clusterip 
