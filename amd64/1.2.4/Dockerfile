FROM gcr.io/google_containers/hyperkube-amd64:$K8S_VERSION


RUN mkdir -p /opt/cni/bin \
  && curl -L https://github.com/appc/cni/releases/download/$CNI_VERSION/cni-$CNI_VERSION.tgz | tar zxv -C /opt/cni/bin \
  && chmod +x /opt/cni/bin/*
  
RUN curl -L https://github.com/projectcalico/calico-cni/releases/download/$CALICOCNI_VERSION/calico -o /opt/cni/bin/calico \
  && curl -L https://github.com/projectcalico/calico-cni/releases/download/$CALICOCNI_VERSION/calico-ipam -o /opt/cni/bin/calico-ipam \
  && chmod +x /opt/cni/bin/*

RUN curl -L http://storage.googleapis.com/kubernetes-release/release/${K8S_VERSION}/bin/linux/amd64/kubectl -o /usr/bin/kubectl \
  && chmod +x /usr/bin/kubectl

RUN curl -L https://github.com/projectcalico/calico-containers/releases/download/${CALICO_VERSION}/calicoctl -o /usr/bin/calicoctl \
 && chmod +x /usr/bin/calicoctl

