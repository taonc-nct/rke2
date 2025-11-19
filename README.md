# Check ebpf
bpftool feature
bpftool prog
mount | grep bpf
bpftool version


zgrep -E "CONFIG_BPF=|CONFIG_BPF_SYSCALL=|CONFIG_NET_CLS_BPF=|CONFIG_BPF_JIT=|CONFIG_NET_CLS_ACT=|CONFIG_NET_SCH_INGRESS=|CONFIG_CRYPTO_SHA1=|CONFIG_CRYPTO_USER_API_HASH=|CONFIG_CGROUPS=|CONFIG_CGROUP_BPF=|CONFIG_PERF_EVENTS=|CONFIG_SCHEDSTATS=" /boot/config-$(uname -r)

grep -E "CONFIG_VXLAN|CONFIG_GENEVE|CONFIG_FIB_RULES|CONFIG_VXLAN|CONFIG_GENEVE|CONFIG_FIB_RULES" /boot/config-$(uname -r)



grep -E "CONFIG_NETFILTER_XT_TARGET_TPROXY|CONFIG_NETFILTER_XT_TARGET_MARK|CONFIG_NETFILTER_XT_TARGET_CT|CONFIG_NETFILTER_XT_MATCH_MARK|CONFIG_NETFILTER_XT_MATCH_SOCKET" /boot/config-$(uname -r)

grep -E "CONFIG_NET_SCH_FQ|CONFIG_NETKIT" /boot/config-$(uname -r)



 /var/lib/rancher/rke2/server/manifests/rke2-cilium-config.yaml

sudo mkdir -p /var/lib/rancher/rke2/server/manifests

sudo tee /var/lib/rancher/rke2/server/manifests/rke2-cilium-config.yaml > /dev/null <<EOF
apiVersion: helm.cattle.io/v1
kind: HelmChartConfig
metadata:
  name: rke2-cilium
  namespace: kube-system
spec:
  valuesContent: |-
    kubeProxyReplacement: true
    k8sServiceHost: "127.0.0.1"
    k8sServicePort: "6443"
    operator:
      replicas: 1
EOF


mkdir -p /etc/rancher/rke2/

sudo tee /etc/rancher/rke2/config.yaml > /dev/null <<EOF
cni: cilium
disable:
- rke2-ingress-nginx
- rke2-metrics-server
- rke2-kube-proxy
- rke2-runtimeclasses
- rke2-snapshot-controller
- rke2-snapshot-controller-crd
EOF


alias kubectl=/var/lib/rancher/rke2/bin/kubectl
export KUBECONFIG=/etc/rancher/rke2/rke2.yaml



mkdir  /var/lib/rancher/rke2/agent/images
curl https://github.com/rancher/rke2/releases/download/v1.33.1%2Brke2r1/rke2-images.linux-amd64.tar.zst -O  /var/lib/rancher/rke2/agent/images/rke2-images-amd64.tar.zst


mkdir -p  /var/lib/rancher/rke2/agent/images/



mkdir -p /var/lib/rancher/rke2/server/manifests/

cat << EOF >  /var/lib/rancher/rke2/server/manifests/rke2-cilium-config.yaml
apiVersion: helm.cattle.io/v1
kind: HelmChartConfig
metadata:
  name: rke2-cilium
  namespace: kube-system
spec:
  valuesContent: |-
    kubeProxyReplacement: true
    k8sServiceHost: 127.0.0.1
    k8sServicePort: 6443
    
EOF

---
apiVersion: catalog.cattle.io/v1
kind: ClusterRepo
metadata:
  name: cilium
spec:
  url: https://helm.cilium.io
---
apiVersion: helm.cattle.io/v1
kind: HelmChart
metadata:
  name: cilium
  namespace: kube-system
spec:
  targetNamespace: kube-system
  createNamespace: false
  version: v1.18.0
  chart: cilium
  repo: https://helm.cilium.io
  bootstrap: true
  valuesContent: |-
  # paste your Cilium values here:
    k8sServiceHost: 127.0.0.1
    k8sServicePort: 6443
    kubeProxyReplacement: true

EOF



sudo setenforce 0





coredns error: coredns Readiness probe failed: HTTP probe failed with statuscode: 503 => check firewalld nhas
Cilium mặc định là triển khai 2 pod => sửa replicas của nó đi nhá.
