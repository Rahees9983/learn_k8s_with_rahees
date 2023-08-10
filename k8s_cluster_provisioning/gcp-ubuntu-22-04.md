Install Kubernetes Cluster using kubeadm on Ubuntu 22.04 LTS (Deployed on GCP)









yum install -y -q kubeadm kubelet kubectl


yum install -y -q kubeadm=1.22.2-00 kubelet=1.22.2-00 kubectl=1.22.2-00

sudo yum install -y kubeadm-1.22.0 kubectl-1.22.0

sudo yum install -y kubeadm-1.22.0 kubectl-1.22.0
sudo yum install -y kubelet-1.22.0

sudo yum install docker-ce-3:24.0.0-1.el8 docker-ce-cli-3:24.0.0-1.el8 containerd.io docker-buildx-plugin docker-compose-plugin




VERSION_STRING=5:20.10.20~3-0~ubuntu-jammy
sudo apt-get install docker-ce=$VERSION_STRING docker-ce-cli=$VERSION_STRING containerd.io docker-buildx-plugin docker-compose-plugin

apt update && apt install -y kubeadm=1.22.2-00 kubelet=1.22.2-00 kubectl=1.22.2-00



Commands to execute on Master node 

    3  ufw disable
    4  swapoff -a; sed -i '/swap/d' /etc/fstab
    5  cat >>/etc/sysctl.d/kubernetes.conf<<EOF
        net.bridge.bridge-nf-call-ip6tables = 1
        net.bridge.bridge-nf-call-iptables = 1
         EOF
    9  sysctl --system
   10  {   apt install -y apt-transport-https ca-certificates curl gnupg-agent software-properties-common;   curl -fsSL https://download.docker.com/linux/ubuntu/gpg | apt-key add -;   add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable";   apt update;   apt install -y docker-ce=5:20.10.20~3-0~ubuntu-jammy containerd.io; }


   12  apt-cache madison docker-ce | awk '{ print $3 }'
   13  VERSION_STRING=5:20.10.20~3-0~ubuntu-jammy
   14  sudo apt-get install docker-ce=$VERSION_STRING docker-ce-cli=$VERSION_STRING containerd.io docker-buildx-plugin docker-compose-plugin
   15  sudo docker run hello-world
   16  docker ps -a
   17  docker images
   18  kubeadm
   19  kubeadm version
   20  curl -sL https://git.io/scue-k8s-remove.sh | sh # WILL BE USED ON CENTOS7 TO REMOVE kubeadm, kubelet and kubectl 
   21  kubeadm version
   22  kubeadm reset --force
   23  yum remove -y kubeadm kubectl kubelet kubernetes-cni kube*
   24  apt remove -y kubeadm kubectl kubelet kubernetes-cni kube*
   25  apt autoremove -y
   26  [ -e ~/.kube ] && rm -rf ~/.kube
   27  [ -e /etc/kubernetes ] && rm -rf /etc/kubernetes
   28  [ -e /opt/cni ] && rm -rf /opt/cni
   29  kubeadm version
   30  {   curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -;   echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" > /etc/apt/sources.list.d/kubernetes.list; }
   31  apt update && apt install -y kubeadm=1.22.2-00 kubelet=1.22.2-00 kubectl=1.22.2-00
   32  kubelet version
   33  kubelet --version
   34  systemctl status kubelet
   35  kubeadm init --apiserver-advertise-address=172.16.16.100 --pod-network-cidr=192.168.0.0/16  --ignore-preflight-errors=all --v=5
   36  docker ps -a
   37  kubectl get no
   38  mkdir -p $HOME/.kube
   39  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
   40  sudo chown $(id -u):$(id -g) $HOME/.kube/config

   43  kubectl --kubeconfig=/etc/kubernetes/admin.conf create -f https://docs.projectcalico.org/v3.18/manifests/calico.yaml
   44  kubectl get nodes
   45  kubectl get node

   46 kubeadm token create --print-join-command


COMMANDS TO EXECUTE ON THE WORKER NODE

	1. COPY THE CONTENT OF THE kubeadm token create --print-join-command EXECUTED ON THE MASTER NODE ON THE WORKER NODE
	 eg. 
	 kubeadm join 172.16.16.100:6443 --token gh20zl.nq4jl9a0ejfadgme --discovery-token-ca-cert-hash        sha256:e280d6bacdc7de2ab57325b1984b9e9995a6b3e86a70656e797efab2310828b5
