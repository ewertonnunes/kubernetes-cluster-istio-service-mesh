# infrastructure
k8s + Pipeline Deploy + Github + Jenkins

# My Laboratory with Kubernetes istio Gateway and how to install istio.

##Git configurations
git init
git config --global user.name "Ewerton Florêncio"
git config --global user.email "ewerton.florencio@outlook.com"
git config --global color.ui true
git config --global core.editor emacs
git status
git pull https://github.com/ewertonnunes/infrastructure main
git branch --set-upstream-to=https://github.com/ewertonnunes/infrastructure main
git push  -u https://github.com/ewertonnunes/infrastructure

## Installing Kubernetes Cluster on Ubuntu Server 20.~

##Requirements

#2GB RAM
#2 vCPU

##Ports

#Protocol	Direction	Port Range	Purpose	Used By
#TCP	    Inbound	    6443	    Kubernetes API server	All
#TCP	    Inbound	    2379-2380	etcd server client API	kube-apiserver, etcd
#TCP	    Inbound	    10250	    Kubelet API	Self, Control plane
#TCP	    Inbound	    10259	    kube-scheduler	Self
#TCP	    Inbound	    10257	    kube-controller-manager	Self

##Worker node(s)
#Protocol	Direction	Port Range	Purpose	Used By
#TCP	Inbound	10250	Kubelet API	Self, Control plane
#TCP	Inbound	30000-32767	NodePort Services†	All

#Disable SWAP
swapoff -a


#Disable IPV6
sudo sysctl -w net.ipv6.conf.all.disable_ipv6=1
sudo sysctl -w net.ipv6.conf.default.disable_ipv6=1
sudo sysctl -w net.ipv6.conf.lo.disable_ipv6=1


#Check Mac-Addresss
cat /sys/class/dmi/id/product_uuid

#Enable IPTABLES Bridge Traffic
cat <<EOF | tee /etc/modules-load.d/k8s.conf
br_netfilter
EOF

cat <<EOF | tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
 sysctl --system

## Update and Install utils on S.O
 apt-get update 
 apt-get install -y apt-transport-https ca-certificates curl net-tools

#Google Cloud public signing key
 curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg

#Set Kubernetes sources repo
echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | tee /etc/apt/sources.list.d/kubernetes.list

#Install kubelet kubeadm kubectl and set mark to keep version in case of System update.
 apt-get update
 apt-get install -y kubelet kubeadm kubectl

#Before install docker
apt-get remove docker docker-engine docker.io containerd runc

#Install utils 
 apt-get update
 apt-get install \
    ca-certificates \
    curl \
    gnupg \
    lsb-release
#Add Docker GPG Key    
 curl -fsSL https://download.docker.com/linux/ubuntu/gpg | gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

##Setup repositories for docker
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | tee /etc/apt/sources.list.d/docker.list > /dev/null

#Install Docker
apt-get update
apt-get install docker-ce docker-ce-cli containerd.io -y

##Configure Docker Daemon to use systemd for management cgroups
 mkdir /etc/docker
cat <<EOF | tee /etc/docker/daemon.json
{
  "exec-opts": ["native.cgroupdriver=systemd"],
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "100m"
  },
  "storage-driver": "overlay2"
}
EOF
##Enable docker on startup

 systemctl enable docker
 systemctl daemon-reload
 systemctl restart docker

##Update and upgrade SO
apt-get update && apt-get upgrade
apt-mark hold kubelet kubeadm kubectl

#Creating and configuring cluster with kubeadm init
kubeadm init
#ou
kubeadm config images pull

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config
##OBS: needs to copy admin.conf from master to worker

export KUBECONFIG=/etc/kubernetes/admin.conf

##Install calico network

curl https://docs.projectcalico.org/manifests/calico.yaml -O
kubectl apply -f calico.yaml


##Command to join with workers
kubeadm join 192.168.15.24:6443 --token d4rynq.qcxcxczjagmhzz2l \
        --discovery-token-ca-cert-hash sha256:fa42427e0d85538c0609f559d47c12d58355be74a86088b3242621d057919103


###Auto completion
source /etc/bash_completion 
source <(kubectl completion bash)













