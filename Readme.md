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

## Pre Reqs

Produtivity

```sh
echo "source <(kubectl completion bash)" >> ~/.bashrc
alias k=kubectl
complete -o default -F __start_kubectl k
export dry='--dry-run=client -o=yaml'
export oy='-o=yaml'
alias kn='kubectl config set-context --current --namespace '
export ETCDCTL_API=3
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
1. kind
1. metadata
1. spec

```sh
kubectl run nginx --image nginx

kubectl run nginx --image nginx --dry-run=client -o yaml > niginx.yaml
```

## Replication Controller

Old version of replica set. Responsible to scaling pods.

1. apiVersion
1. kind
1. metadata
1. spec
    1. template - All pod manifest wihtout apiVersion and kind
    1. replica - number of pods

Eg

```yaml
apiVersion: v1
kind: ReplicationController
metadata:
  name: rc-nginx
  labels:
    type: rc
    tier: web
spec:
  template:
    metadata:
      name: nginx
      labels:
        tier: web
    spec:
      containers:
        - name: nginx
          image: nginx
  replicas: 3
  ```

## ReplicaSet

Like replication controller with one difference. We can select pods with selector. This mechanism can be used to running pods.

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nginx-rs
  labels:
    tier: web
spec:
  template:
    metadata:
      name: nginx-rs
      labels:
        tier: web
        parent: nginx-rs
    spec:
      containers:
        - name: nginx
          image: nginx
  replicas: 3
  selector:
    matchLabels:
      parent: nginx-rs
```

Command to scale:

1. Edit the manifest and replace

    ```sh
    kubectl replace -f manifests/replicaset.yaml
    ```

1. Scale with point to manifest filename:

   ```sh
    kubectl scale --replicas=6 -f manifests/replicaset.yaml
    ```

1. Scale with replicaset name

   ```sh
    kubectl scale --replicas=6 replicaset nginx-rs
    ```

## Deployment

Can create versions of application. Same manifest as Replicaset except the kind.

```sh
kubectl create deployment nginx-deploy --image=nginx --replicas=3 --dry-run=client -o yaml > manifests/deployment.yaml
```

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deploy
  labels:
    tier: web
spec:
  template:
    metadata:
      name: nginx-deploy
      labels:
        tier: web
        parent: nginx-deploy
    spec:
      containers:
        - name: nginx
          image: nginx
  replicas: 3
  selector:
    matchLabels:
      parent: nginx-deploy
```

## Services

Objects that enables communication between pods. Can be one of 3 types:

1. Nodeport - Exposes only a port to external access
1. Clientip - Provides loadbalancer to internal services
1. Loadbalancer - Provides loadbalancer to external acess

Creating service

```sh
kubectl create svc nodeport nginx-svc --node-port=30080 --tcp=8080:8080 --dry-run=client -o yaml > manifests/service.yaml
```

Eg.

```yaml
apiVersion: v1
kind: Service
metadata:
  labels:
    app: nginx-svc
  name: nginx-svc
spec:
  ports:
    - name: 8080-8080
      nodePort: 30080
      port: 8080
      protocol: TCP
      targetPort: 8080
  selector:
    app: nginx-svc
  type: NodePort

```

## Namespace

Utilized to separate K8s objects. Like folder.

Dns structure:

```sh
<service-name>.<namespace>.<object-type>.<default domain name>
Eg: nginx.dev.svc.cluster.local
```

Creating namespace:

```sh
kubectl create namespace dev
```

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: dev
```

Listing objects for namespace "dev":

```sh
kubectl get pod --namespace=dev
```

## Taints and Tolarations

Flag that marks which pods cannot be schedule if has the labels. In case the Pod has tolerations to these taint, it will be ignored.

```sh
kubectl taint nodes <nodename> key=value:<taint-effect>
```

taint-effect can be:

1. NoSchedule
1. PreferNoSchedule
1. NoExecute

## DaemonSet

Program that is installed in each node. Can be kubelet, log metrics aggregator, istio etc...

How to create?

By default we cannot use the kubectl create command. The manifest between DaemonSet and Deployment are very similar.

```sh
kubectl create deployment myds --image=fluentbit:latest -o yaml > myds.yaml
```

Change the kind and apply the manifest file.

## Static Pod

Feature that permits schedule pod manually.

We can create pod copying manifest file to kube config path.

How find these files?

Check kubelet config file:

```sh
cat /var/lib/kubelet/config.yaml
```

check for **staticPodPath**

By default its is **/etc/kubernetes/manifests**
