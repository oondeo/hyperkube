# hyperkube
Kubernetes image with cni and calico plugins.

Example use

```
export K8S_VERSION=v1.2.4
export ARCH=amd64
# HYPERKUBE_IMAGE="quay.io/coreos/hyperkube:${K8S_VERSION}_coreos.cni.1"
export HYPERKUBE_IMAGE="gcr.io/google_containers/hyperkube:${K8S_VERSION}"
export INFRA_IMAGE="gcr.io/google_containers/pause:3.0"
export ETCD_ENDPOINTS="http://127.0.0.1:4001"
export ADVERTISE_IP="127.0.0.1" #changeme


mount -o noatime,bind /var/lib/kubelet /var/lib/kubelet
mount --make-shared /var/lib/kubelet
docker run -d \
      --restart always --name kubemaster \
      -e ETCD_ENDPOINTS="${ETCD_ENDPOINTS}" -e HOSTNAME="${ADVERTISE_IP}" -e IP="${ADVERTISE_IP}"\
      -e CALICO_CTL_CONTAINER='TRUE' -e CALICO_NETWORKING='true' \
      -e FELIX_FELIXHOSTNAME="${ADVERTISE_IP}" -e FELIX_ETCDENDPOINTS="${ETCD_ENDPOINTS}" \
      --volume=/dev:/dev:rw \
      --volume=/etc/localtime:/etc/localtime:ro \
      --volume=/:/rootfs:rw \
      --volume=/sys:/sys:rw \
      --volume=/etc/kubernetes/cni:/etc/kubernetes/cni:rw \
      --volume=/etc/ssl/certs:/etc/ssl/certs:ro \
      --volume=/var/lib/docker:/var/lib/docker:rw \
      --volume=/var/lib/kubelet:/var/lib/kubelet:rw \
      --volume=/var/log:/var/log:rw \
      --volume=/var/run:/var/run:rw \
      --net=host \
      --pid=host \
      --privileged \
      ${HYPERKUBE_IMAGE}  \
      /hyperkube kubelet \
              --pod-infra-container-image=${INFRA_IMAGE} \
              --allow-privileged=true \
              --api-servers=http://127.0.0.1:8080 \
              --config=/etc/kubernetes/manifests \
              --hostname-override=${ADVERTISE_IP} \
              --network-plugin-dir=/etc/kubernetes/cni/net.d \
              --network-plugin=cni \
              --address=0.0.0.0 \
              --cluster-dns=${DNS_SERVICE_IP} \
              --cluster-domain=$DNS_DOMAIN \
              --logtostderr=true \
              --v=${LOGLEVEL}
              
docker exec -ti kubemaster kubectl get pods
```
example kubectl script
```
cat > /usr/local/bin/kubectl <<KUBECTL
#!/bin/bash

docker exec -ti kubemaster sh -c "cd '/rootfs\${PWD}';/usr/bin/kubectl \$* \"

KUBECTL

chmod +x /usr/local/bin/kubectl
```

calicoctl should work in similar way, instead you can use calicoctl container:
```
docker run -it --net=host --privileged --rm \
  -v /dev:/dev:rw -v /sys:/sys:rw \
  -v /etc/localtime:/etc/localtime:ro -v /var/run:/var/run:rw \
  -v /var/log/calico:/var/log/calico:rw -v /lib/modules:/lib/modules:ro \
  -e FELIX_ETCDENDPOINTS=${ETCD_ENDPOINTS} -e FELIX_FELIXHOSTNAME=${ADVERTISE_IP} \
  -e CALICO_NETWORKING=true -e IP=${ADVERTISE_IP} \
  -e ETCD_ENDPOINTS="${ETCD_ENDPOINTS}" -e HOSTNAME="${ADVERTISE_IP}" \
  calico/ctl:${CALICO_VERSION} $*
```


