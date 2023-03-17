---
title: kubernetes
date: "2020-12-12T19:06:15+0800"
draft: false
tags: ["k8s"]
---
[Kubernetes vs. Docker: What Does it Really Mean?](https://www.sumologic.com/blog/kubernetes-vs-docker/)

[Kubernetes 101: Pods, Nodes, Containers, and Clusters](https://medium.com/google-cloud/kubernetes-101-pods-nodes-containers-and-clusters-c1509e409e16#:~:text=Unlike%20other%20systems%20you%20may,same%20resources%20and%20local%20network.&text=Pods%20are%20used%20as%20the%20unit%20of%20replication%20in%20Kubernetes.)

[Demystifying Kubernetes Operators with the Operator SDK: Part 1](https://www.linux.com/topic/cloud/demystifying-kubernetes-operators-operator-sdk-part-1/)
[Kubernetes Components](https://kubernetes.io/docs/concepts/overview/components/)

[Kubernetes Cluster vs Master Node](https://www.suse.com/c/kubernetes-cluster-vs-master-node/#:~:text=What%20is%20Master%20Node%20in,the%20frontend%20to%20the%20cluster.)

[api](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.21/#podtemplate-v1-core) 

[kubectl-commands](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create)

[Dive Deep into Resource Requests and Limits in Kubernetes](https://kubesphere.io/blogs/understand-requests-and-limits-in-kubernetes/)

[Kubernetes中pod的创建流程](https://cloud.tencent.com/developer/article/1748758)

[10 张图带你搞懂 Kubernetes Pod 的创建流程](https://z.itpub.net/article/detail/531D698361F67B28E49B23552FDA3A9F)

[kubectl 创建 Pod 背后到底发生了什么？](http://dockone.io/article/9134)

## what is kubernetes?

![](https://assets-www.sumologic.com/application-content/life_of_application_docker_kubernetes.png?mtime=20191022143928&focal=none)

docker: create containers
k8s: 管理(编排)容器:
  1. deploy container
  2. scaling up and removing containers
  3. monitor  container

## architecture

![juNNbi](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210227/juNNbi.jpg)
![ruLhgE](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210226/ruLhgE.jpg)
![](https://assets-www.sumologic.com/application-content/kubernetes_cluster.png?mtime=20191022144431&focal=none)


1. master: manager worker
2. worker: 运行container 


### 1. master node(control plane)

1. etcd: 集群数据
2. kube-scheduler: 分配new pod 到node 上
3. apiserver: 提供对外和对内交流的枢纽 
4. controller: **a state machine**; watch  the state of  your cluster,   move the state to the desired state
   - Node controller: Responsible for noticing and responding when nodes go down.
   - Replication controller: Responsible for maintaining the correct number of pods for every replication controller object in the system.

### 2. Work node

1. Kubelet
   1. manage pods
2. kube proxy
    1. manage network;

3. pod: k8s编排的对象；

## pod

![](https://d33wubrfki0l68.cloudfront.net/fe03f68d8ede9815184852ca2a4fd30325e5d15a/98064/docs/tutorials/kubernetes-basics/public/images/module_03_pods.svg)

k8s管理的基本单位；
a pod  is a group of container which share storage  and network resoureces;

### 1. workload 
用户部署的应用，可能有多个pod组成；

1. pods
2. deployment and replicaSet: 复制N份pod;;
      deployment--> replicaSet--> pods
      replicaSet---> pods
3. statefulset: 有状态应用
4. daemonset: 运行在每个node 上
5. job: 运行N次后退出
6. cornJob:定时运行 


  
###  2.how to create;

![ichJj8](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20211206/ichJj8.jpg)

1. kubuctl send command to  apiserver; 
2. apisever save yaml to etcd;
3. controller watch etcd, 生成pod配置信息, save to etcd;
4. scheduler watch etcd pod配置信息，指定相关node创建pod；
5. 指定node.kubelet创建pod;


### 2.lifeCycle
1. pending: 等待被创建；
2. running: pod所有容器已经被创建，至少一个容器正在运行或者正在启动
3. failed:pod所有容器都终止，至少一个 是非0退出；
4. unkonwon:无法获取pod状态


## yaml

yaml decribe  a  k8s  resouce state, the k8s will work constantly  to ensure the state;
resource type

```yaml
 apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 2 # tells deployment to run 2 pods matching the template
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80
        request:
        limit:

```

### kind

define a k8s object:

   1. workload
   2. service
   3. storage

### metadata

    help to uniquely identify the object;
    1. name:
    2. namespace:
    3. lables： are meaningful and relevant to users, but do not directly imply semantics to the core system.

### spec

spec:  what state you desire for the obejct

 1. replicas:
 2. selector:  select pods
 3. spec.template: the actual template for the container

### template

#### resouce

```yaml
    resources:
            limits:
              cpu: '2'
              memory: 1Gi
            requests:
              cpu: 500m
              memory: 128Mi
```

memory: Mi,Gi
cpu: 100m, 0.1;  (0.1cpu core)

cpu compressible; ram incompressible;

1. request: used to schedule, if there are no node satisfy the resources the pod won't be scheduled;
    ![OUkI1C](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210718/OUkI1C.png)

    2 cpu cluster max request  2cpu
    2g ram  cluster max request 2g

2. limit: moniter the runing, if reach;
   1. cpu:  be restricted, continue run;
   2. memroy: oom(out of memory) error, pod restart;

3. the used resouce exceed the node's resouce;
    1. cpu:
        1. the pod run normaly, but  the pod's load average  will increase;  
    2. ram:
        1. pod will evicted from current node by  a order
            1. request && limit = 0
            2. at least one container in pod have reqeust specified for cpu or memory
            3. every container request == limit

## kubectl  command

command type:

1. manage resouces
2. debug/inspect

### 1. manage resouce

delete a deployment

for controller resourxe, you  should  delete the controller, otherwise the pods will recreate by the controller

```shell
  kubectl delete -f a.yaml
  kubectl delete delopyment  hellogo-deployment 
```

### 2. inspect/debug

1. exec a command in a  container

kubectl exec (POD | TYPE/NAME) [-c CONTAINER] [flags] -- COMMAND [args...]

```shell
kubect  exec centos-container -it  --- bash
```

## scale

cluster-autoscaler
![upfbaf](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210721/upfbaf.jpg)

### HorizontalPodAutoscaler

1. monitor cpu, memory, and if need scale

```c
kind:

cpu

```

