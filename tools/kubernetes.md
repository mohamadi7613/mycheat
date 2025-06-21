# Kubernetes (K8s)


### Kubernetes featuers
+ Kubernetes in a container orchestration tool
+ open source by goole
+ alternatives: 1. docker swarm (lacks some advanced feathers) 2. MESOS by Apache(difficult to set up but supprts many advanced featuers)


### Kubernetes Architecture

##### Concepts
1. node: node is a machine (phisical or virtual)
2. cluster: cluster is a group of nodes
3. master node: (also known as control panel) is a node who is responsible for orchestration of other nodes
4. worker node: (also known as minion) worker nodes execute the workloads
5. pods: Pods are the smallest deployable units in Kubernetes


##### components
1. Master Components
    a. API server:     exposes the Kubernetes API
    b. etcd:           a key-value store that holds all cluster data, including configurations, states, and other metadata.
    c. scheduler:      Assigns newly created pods to nodes
    d. controller:     reponsible when nodes goes down and reponsible to bring up new containers
2. Worker Components
    a. kubelet:     An agent that runs on each node and ensures that containers are running in a pod, communicating with API server in master
    b. container runtime: the software responsible for running containers. softwares like Docker, Rocket, Cryo


### commands

```bash
kubectl run hello-minikube           # kubctl or kube command or kube control
kubectl clusterinfo
kubectl get nodes
```














































