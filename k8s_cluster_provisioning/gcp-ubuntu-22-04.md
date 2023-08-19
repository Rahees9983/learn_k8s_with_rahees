# Install Kubernetes Cluster using kubeadm on Ubuntu 22.04 LTS (Deployed on GCP)
Follow this documentation to set up a Kubernetes cluster with one master and one worker node on __Ubuntu 22.04 LTS VM Provisoined on GCP__.

## Kubernetes Cluster Requirments
|Role|Public IP |Private IP|OS|Machine type|Service Acount|
|----|----|----|----|----|----|
|Master|Yes|10.128.0.55|Ubuntu 22.04|e2-medium|Compute Engine default service account|
|Worker|Yes|10.128.0.56|Ubuntu 22.04|e2-medium|Compute Engine default service account|

## Command to be executed on master and worker nodes are below:-
##### Login as `root` user
```
sudo su -
```
Perform all the commands as root user unless otherwise specified
##### Disable Firewall
```
ufw disable
```
##### Disable swap
```
swapoff -a; sed -i '/swap/d' /etc/fstab
```
##### Update sysctl settings for Kubernetes networking
```
cat >>/etc/sysctl.d/kubernetes.conf<<EOF
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
sysctl --system
```
##### Install docker engine 
```
{
  apt install -y apt-transport-https ca-certificates curl gnupg-agent software-properties-common
  curl -fsSL https://download.docker.com/linux/ubuntu/gpg | apt-key add -
  add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
  apt update
  apt install docker-ce=5:20.10.20~3-0~ubuntu-jammy docker-ce-cli=5:20.10.20~3-0~ubuntu-jammy containerd.io docker-buildx-plugin docker-compose-plugin
}
```
### Kubernetes Setup
##### Add Apt repository
```
{
  curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -
  echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" > /etc/apt/sources.list.d/kubernetes.list
}
```
##### Install Kubernetes components
```
apt update && apt install -y kubeadm=1.22.2-00 kubelet=1.22.2-00 kubectl=1.22.2-00
```

## On kmaster
##### Initialize Kubernetes Cluster
Update the below command with the ip address of kmaster
```
kubeadm init --apiserver-advertise-address=10.128.0.55 --pod-network-cidr=192.168.0.0/16  --ignore-preflight-errors=all --v=5
```
##### Deploy Calico network
```
kubectl --kubeconfig=/etc/kubernetes/admin.conf apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.26.1/manifests/calico.yaml
```

##### Cluster join command
```
kubeadm token create --print-join-command
```

##### To be able to run kubectl commands as non-root user
If you want to be able to run kubectl commands as non-root user, then as a non-root user perform these
```
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

## On Kworker
##### Join the cluster
Use the output from __kubeadm token create__ command in previous step from the master server and run here.

## Verifying the cluster (On kmaster)
##### Get Nodes status
```
kubectl get nodes
```
##### Get component status
```
kubectl get cs
```
Enjoy Kubernetes Learning with Rahees!!!
