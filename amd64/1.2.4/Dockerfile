ENV K8S_VERSION=v1.2.4 CALICOCNI_VERSION=v1.3.1 CNI_VERSION=v0.3.0

FROM gcr.io/google_containers/hyperkube-amd64:$K8S_VERSION


RUN mkdir -p /opt/cni/bin \
  && curl -L https://github.com/appc/cni/releases/download/$CNI_VERSION/cni-$CNI_VERSION.tgz | tar zxv -C /opt/cni/bin \
  && curl -L https://github.com/projectcalico/calico-cni/releases/download/$CALICOCNI_VERSION/calico -o /opt/cni/bin/calico \
  && curl -L https://github.com/projectcalico/calico-cni/releases/download/$CALICOCNI_VERSION/calico-ipam -o /opt/cni/bin/calico-ipam \
  && chmod +x /opt/cni/bin/*
