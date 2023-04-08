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



