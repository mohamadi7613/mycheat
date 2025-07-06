# Kubernetes (K8s)


### Kubernetes featuers
+ Kubernetes in a container orchestration tool
+ open source by goole
+ alternatives: 1. docker swarm (lacks some advanced feathers) 2. MESOS by Apache(difficult to set up but supprts many advanced featuers)



### Kubernetes Architecture

##### Concepts
1. node(minion): node is a machine (phisical or virtual)
2. cluster: cluster is a group of nodes
3. master node: (also known as control panel) is a node who is responsible for orchestration of other nodes
4. worker node: (also known as minion) worker nodes execute the workloads
5. pods: Pods are the smallest deployable units in Kubernetes. It represents a single or group of containers.


##### components
1. Master Components
    a. API server:     exposes the Kubernetes API (kube-apiserver)
    b. etcd:           a key-value store that holds all cluster data, including configurations, states, and other metadata.
    c. scheduler:      Assigns newly created pods to nodes
    d. controller:     reponsible when nodes goes down and reponsible to bring up new containers
2. Worker Components
    a. kubelet:     An agent that runs on each node and ensures that containers are running in a pod, communicating with kube-apiserver in master
    b. container runtime: the software responsible for running containers. softwares like Docker, Rocket, Cryo



### commands

```bash
kubectl run hello-minikube           # kubctl (kube command,kube control) is used to deploy and manage apps on k8s
kubectl clusterinfo                 # information about the cluster
kubectl get nodes                    # list all the nodes of cluster
```



###  Evolution of Container Runtimes

1. Docker was the dominant container runtime.
2. Kubernetes initially only supported Docker.
3. CRI (Container Runtime Interface) is a standard API that was introduced to support other runtimes (e.g., containerd, rkt).
4. OCI (Open Container Initiative) is the standard to ensure interoperability and portability of containers. It was founded by Docker and other major players in 2015 under the Linux Foundation.
> OCI Standards:
    1. Image Spec (how images are built).
    2. Runtime Spec (how containers run).
5. Docker Shim: A hack to keep Docker working in Kubernetes (deprecated in Kubernetes 1.24).
6. Containerd: It is part of docker but also a separated project which is a lightweight, CNCF-graduated runtime

##### K8s Container Runtimes
1. Docker:	Full-featured (CLI, API, build tools, runc runtime).
2. containerd:	just comes with `ctr`  command which supports limited features.
3. CRI-O:	Kubernetes-native runtime (for OpenShift, etc.).

##### CLI Tools Comparison
1. docker	Full container management.
2. ctr	    Debugging containerd.
3. nerdctl	Docker-like CLI for containerd (recommended alternative for ctr).
4. crictl	Debug any CRI runtime (Kubernetes-focused).

```bash
ctr  images pull docker.io/library/nginx:latest
ctr run docker.io/library/nginx:latest nginx
nerdctl run --name nginx -p 8080:80 nginx               # nerdctl supports docker flags
nerdctl ps
crictl ps -a                            # list all containers
crictl images                             # list all images
crictl pods                             # list all pods
```












































