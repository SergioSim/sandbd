---
layout: post
title: Kubernetes Introduction
categories: kubernetes
---

Here are some notes we took while reading some of the Kubernetes introduction &
documentation.

# Introduction to Kubernetes

Kubernetes is a popular open-source container-orchestration platform.
It facilitates running distributed systems resiliently, taking care of scaling,
load-balancing, failover, and much more.

## Architecture

Retrieved from: [https://kubernetes.io/docs/concepts/overview/components/](https://kubernetes.io/docs/concepts/overview/components/)

A Kubernetes cluster comprises two main parts - the Control Plane and worker nodes.

The Control Plane is composed of the following primary services:
- `kube-apiserver`
  - It consists of a REST front-end.
    Provides an entry point for clients to interact with the Control Plane, configure the
    Kubernetes cluster and perform other tasks.
- `etcd`
  - It consists of a distributed and consistent key-value store.
  - It holds cluster state-related data (serves a similar purpose as Apache Zookeeper).
- `kube-scheduler`
  - Decides on which cluster node to run incoming Pods (see worker node section).
- `kube-controller-manager`
  - Watches the state of the cluster nodes, jobs, etc., and makes or requests changes when needed by interacting with the `kube-apiserver`.
- `cloud-controller-manager`
  - It links the cluster with the cloud provider API.

The worker node is composed of the following primary services:
- `kubelet`
  - It monitors the health of containers running in a `Pod` and communicates with the
    Control Plane (heartbeats).
  - It can register itself with the `kube-apiserver` (`--register-node` flag)
  - Note: when the worker node configuration is updated, it is a good practice to
    re-register it with the `kube-apiserver` to ensure that all `Pods` will be drained,
    evicted, and re-scheduled.
- `kube-proxy`
  - It is a network proxy that maintains network rules.
  - It watches the `kube-apiserver` for the addition, updates, and removal of Services
    and endpoints
- `Container runtime`
  - It is the software that runs containers. (E.g., `containerd`, `CRI-O`)

## Kubernetes objects

Kubernetes objects represent the cluster's desired state.
They describe what applications are running, the available resources, and policies.
[https://kubernetes.io/docs/concepts/overview/working-with-objects/kubernetes-objects/](
 https://kubernetes.io/docs/concepts/overview/working-with-objects/kubernetes-objects/)

### Node

It represents a virtual or physical machine (control plane or worker node) uniquely
identified by a name.
[https://kubernetes.io/docs/concepts/architecture/nodes/](
 https://kubernetes.io/docs/concepts/architecture/nodes/)

### Namespace

It divides cluster resources between multiple users.
[https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/](
 https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/)

### Pod

It is a group of one or more containers with shared storage and network resources.
[https://kubernetes.io/docs/concepts/workloads/pods/](
 https://kubernetes.io/docs/concepts/workloads/pods/)

Example pod definition (nginx.yaml):

```{yaml}
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - name: nginx
    image: nginx:1.23.2
    ports:
    - containerPort: 80
```

- To **run** the example pod - run: `kubectl apply -f nginx.yaml`.
- To **update** the pod - edit its definition yaml file and run:
  `kubectl replace --force -f nginx.yaml`
- To **remove** the pod - run: `kubectl delete -f nginx.yaml` or
  `kubectl delete pods nginx`

### ReplicaSet

It maintains a stable set of replica Pods running at any given time.
[https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/](
 https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/)

### Deployment

It consists of ReplicaSet running pods with more features. It's the recommended way to
run Pods.
[https://kubernetes.io/docs/concepts/workloads/controllers/deployment/](
 https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)

Example Deployment definition (nginx-deployment.yaml):

```{yaml}
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.23.2
        ports:
        - containerPort: 80
```

### DaemonSet

A DaemonSet ensures that all (or some) Nodes run a copy of a Pod.
Useful for tasks like node monitoring, cluster storage, and log collection.
[https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/](
 https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/)

### Service

It logically groups Pods, ReplicaSets, Deployments, DaemonSets, and StatefulSets via
Labels and Selectors and defines a policy to access them.
[https://kubernetes.io/docs/concepts/services-networking/service/](
 https://kubernetes.io/docs/concepts/services-networking/service/)

### Volume

It provides various means of data storage.
[https://kubernetes.io/docs/concepts/storage/volumes/](
 https://kubernetes.io/docs/concepts/storage/volumes/)
[https://kubernetes.io/docs/concepts/storage/persistent-volumes/](
 https://kubernetes.io/docs/concepts/storage/persistent-volumes/)

### ConfigMap

It provides containers in a pod with environment variables, command-line arguments, and
configuration files (< 1 MB).
[https://kubernetes.io/docs/concepts/configuration/configmap/](
 https://kubernetes.io/docs/concepts/configuration/configmap/)
[https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/](
 https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/)

### Secret

It enables the sharing of sensitive data with containers running in a pod.

- Example:
  `kubectl create secret generic some-password --from-literal=password=notsomepassword`

[https://kubernetes.io/docs/concepts/configuration/secret/](
 https://kubernetes.io/docs/concepts/configuration/secret/)
[https://kubernetes.io/docs/tasks/configmap-secret/](
 https://kubernetes.io/docs/tasks/configmap-secret/)

## Installing `minikube` on Ubuntu

Minukube is a handy tool to quickly set up a local Kubernetes cluster.

### Prerequisites

#### Check for virtualization support

The following command should show matches for `svm` or `vmx`.

```{bash}
egrep "svm|vmx" /proc/cpuinfo
```

#### Install `VirtualBox`

See the [official VirtualBox documentation](https://www.virtualbox.org/wiki/Downloads).

### Download and install the `minikube` Debian package

```{bash}
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube_latest_amd64.deb
sudo dpkg -i minikube_latest_amd64.deb
```

### Start `minikube`

The following command will download, set up, and start a VirtualBox virtual machine with
a running single-node all-in-one Kubernetes installation.

```{bash}
minikube start --driver virtualbox
```

### Get `minikube` status/profile/node/service/ip information

```{bash}
minikube status
minikube profile list
minikube node list
minikube service --all
minikube service nginx
minikube ip
```

### Stop `minikube`

```{bash}
minikube stop
```

### Delete `minikube`

```{bash}
minikube delete
```

### Enable and start the Kubernetes Dashboard & Metrics addons

```{bash}
minikube addons list
minikube addons enable metrics-server
minikube addons enable dashboard
minikube dashboard
```

### More `minikube` commands

See [official minikube commands documentation](https://minikube.sigs.k8s.io/docs/commands/).

## kubectl

`kubectl` is a CLI for interacting with Kubernetes.
See the [official kubectl reference](https://kubernetes.io/docs/reference/kubectl/).

### Show the `kubectl` configuration file

```{bash}
kubectl config view
cat $HOME/.kube/config
```

### Show Kubernetes cluster information

```{bash}
kubectl cluster-info
```

### Show Kubernetes API resources

```{bash}
kubectl api-resources
```

### List Kubernetes objects

```{bash}
kubectl get nodes
kubectl get no

kubectl get namespaces
kubectl get ns

kubectl get pods --all-namespaces -o wide
kubectl get pods -A -o wide
kubectl get po nginx -o yaml

kubectl get replicasets
kubectl get rs

kubectl get deployments
kubectl get deploy

kubectl get endpoints
kubectl get ep

kubectl get services
kubectl get svc

kubectl get secrets

kubectl get configmaps
kubectl get cm

kubectl get ingresses
kubectl get ing

# Example with multiple resources and label filtering
kubectl get no,ns,po,rs,deploy,svc -l app=nginx --show-labels
kubectl get all
```

### Get more information about objects

```{bash}
kubectl describe pods PODNAME
```

### Scale a deployment

```{bash}
kubectl scale deploy nginx-deployment --replicas=4
```

### Update image version of an deployment

```{bash}
kubectl set image deploy nginx-deployment nginx=nginx:1.22.1
```

### Get information about the deployment rollout update

```{bash}
kubectl rollout history deploy nginx-deployment
kubectl rollout history deploy nginx-deployment --revision 1
```

### Rollback a deployment

```{bash}
kubectl rollout undo deploy nginx-deployment --to-revision=1
```
