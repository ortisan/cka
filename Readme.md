## Minikube

Install

```sh
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
```

minikube dashboard

## ETCD

Install

```sh
git clone -b v3.4.16 https://github.com/etcd-io/etcd.git
cd etcd
./build
export PATH="$PATH:`pwd`/bin"
```

Test

Start service

```sh
ectd
telnet localhost 2379
```

etcd starts on port **2379**.

```sh
etcdctl put greeting "Hello Etcd"
etcdctl get greeting
```

## Kube API Server

1. Authenticate user
1. Validate Request
1. Retrieve data
1. Update ETCD

To show configs:

```sh
ps -aux | grep kube-apiserver
```

## Kube Controler Manager

Responsible for monitor and remediate problems

To show configs:

```sh
ps -aux | grep kube-controller-manager
```

## Kube-Scheduler

Decide which node can add the new pod.

1. Filter nodes based on physical resources.
1. Rank nodes

To show configs:

```sh
ps -aux | grep kube-scheduler
```

## Kubelet

Sidecar that runs in all nodes.

1. Register node
1. Create pod
1. Monitor node & pods

To show configs:

```sh
ps -aux | grep kubelet
```

## Kube-Proxy

Installed in each node. Responsible to configure network (iptables) for services.

```sh
ps -aux | grep kubelet
```

## Pods

Most granular object of K8s. One pod can have multiples containers.

Manifest contains 4 sections:

1. apiVersion
1. king
1. metadata
1. spec

```sh
kubectl run nginx --image nginx

kubectl run neing --image nginx --dry-run=client -o yaml > niginx.yaml
```
