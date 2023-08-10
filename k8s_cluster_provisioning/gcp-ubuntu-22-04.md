Install Kubernetes Cluster using kubeadm on Ubuntu 22.04 LTS (Deployed on GCP)

Follow this documentation to set up a Kubernetes cluster with one master and one worker node on Ubuntu 22.04 LTS VM Provisoined on GCP.

VM configuration

OS Version:- Ubuntu 22.04 LTS
Architure:- x86
Cloud:- GCP
VM Name:- kmaster
Service account used for VM: Compute Engine default service account
Public Ip: Yes
Private IP:- Yes
Machine type:- e2-medium
Boot Disk size: 10 GB

Command to be executed on master and worker nodes are below:-

$ sudo su -
$  ufw disable
$  swapoff -a; sed -i '/swap/d' /etc/fstab
$  cat >>/etc/sysctl.d/kubernetes.conf<<EOF
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF

$  sysctl --system
$  {   apt install -y apt-transport-https ca-certificates curl gnupg-agent software-properties-common;   curl -fsSL https://download.docker.com/linux/ubuntu/gpg | apt-key add -;   add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable";   apt update; }
$  apt-cache madison docker-ce | awk '{ print $3 }'
$  VERSION_STRING=5:20.10.20~3-0~ubuntu-jammy
$  sudo apt-get install docker-ce=$VERSION_STRING docker-ce-cli=$VERSION_STRING containerd.io docker-buildx-plugin docker-compose-plugin
$  sudo docker run hello-world
$  docker ps -a
$  {   curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -;   echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" > /etc/apt/sources.list.d/kubernetes.list; }
$  apt update && apt install -y kubeadm=1.22.2-00 kubelet=1.22.2-00 kubectl=1.22.2-00

Commands be executed on master node

$  kubeadm init --apiserver-advertise-address=10.128.0.27 --pod-network-cidr=192.168.0.0/16  --ignore-preflight-errors=all --v=5
$  kubectl --kubeconfig=/etc/kubernetes/admin.conf apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.26.1/manifests/calico.yaml
$  kubectl get po -A

Comands to be executed on worker node

To join the worker node in the cluster use the output from **kubeadm token create --print-join-command **
example of my output

$ kubeadm join 10.128.0.27:6443 --token j0iemg.v7y4vt71lyw3pbf3 \
        --discovery-token-ca-cert-hash sha256:bf71e86a1b626a5e854e204a8a182e2f412a9252bba2ed89b8e280200a7334aa

 
