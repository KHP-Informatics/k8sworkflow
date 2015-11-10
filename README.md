# k8sworkflow
Workflows ontop of Kubernettes

- [Hyperkube](https://hub.docker.com/r/jetstack/hyperkube/)
- [JetStack's Getting Started with Kubernettes](https://github.com/jetstack/getting-started-kubernetes)

# k8sworkflow
Workflows ontop of Kubernetes

Goal: Build a workflow management system ontop of Kubernetes
* Run a queueing system as a container

* Requires Kubernetes v1.1.x with experimental support for Jobs

A quick and easy way to run Kubernetes locally is to use the Hyperkube. This is a Docker image that 
contains the Kubernetes binaries. The Kubelet starts these services as containers.

Kubernetes system state is stored in etcd. First step is start an etcd container.

```shell
docker run --net=host -d gcr.io/google_containers/etcd:2.0.9 /usr/local/bin/etcd --addr=127.0.0.1:4001 --bind-addr=0.0.0.0:4001 --data-dir=/var/etcd/data
```

Next step is to run the Kubelet container - with a bunch of switches.

```shell
docker run \
  --net=host \
  --pid=host \
  -d \
  -v /sys:/sys:ro \
  -v /var/run:/var/run:rw \
  -v /:/rootfs:ro \
  -v /var/lib/docker/:/var/lib/docker:rw \
  -v /var/lib/kubelet/:/var/lib/kubelet:rw \
  jetstack/hyperkube:v1.1.1-exp \
  /hyperkube kubelet \
  --api_servers=http://localhost:8080 \
  --v=2 \
  --address=0.0.0.0 \
  --enable_server \
  --hostname_override=127.0.0.1 \
  --config=/etc/kubernetes/manifests \
  --containerized
```

The Kubelet will now launch a master pod containing all the system services (described in /etc/kubernetes/manifests) baked in the Hyperkube image.

Run `docker ps` and you should see Kuernetes containers.

To interact with the cluster, use the `kubectl` CLI tool. It can be downloaded pre-built direct from Google.

```shell
wget https://storage.googleapis.com/kubernetes-release/release/v1.1.1/bin/linux/amd64/kubectl
chmod u+x kubetctl
```
