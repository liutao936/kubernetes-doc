# kubernetes

开始详细部署：
一、master服务器：
首先要关闭防火墙，设置NTP，设置hosts解析；
1、安装docker、kubernetes
cd /etc/yum.repos.d/
wget https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
vim kubernetes.repo

[kubernetes]
name= Kubernetes Repo
name=https://mirrors.aliyun.com/kubernetes/yum/repos/minikube-el7-x86_64/
gpgcheck=1
gpgkey=https://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
enable=1

yum repolist

yum install docker-ce kubelet kubeadm kubectl


2、编辑初始化
vim /usr/lib/systemd/system/docker.service(安装完在注释掉)

#add
Environment="HTTP_PROXY=http://www.ik8s.io:10080"
Environment="NO_PROXY=127.0.0.0/8,172.20.0.0/16"

systemctl daemon-reload
systemctl start docker
docker info
确认打开下面
cat /proc/sys/net/bridge/bridge-nf-call-iptables 
cat /proc/sys/net/bridge/bridge-nf-call-ip6tables 

注：执行docker info出现如下警告
WARNING: bridge-nf-call-iptables is disabled
WARNING: bridge-nf-call-ip6tables is disabled
解决办法：
vi /etc/sysctl.conf
添加以下内容
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
最后再执行
sysctl -p

rpm -ql kubelet (查看安装目录)

设置kubelet和docker开机启动
systemctl enable kubelet
systemctl enable docker

kubeadm init --help (kunbeadm初始化帮助)

vim /etc/sysconfig/kubelet
#add
KUBERLET_EXTRA_ARGS="--fail-swap-on=false"

kubeadm init --kubernetes-version=v1.11.1 --pod-network-cidr=10.244.0.0/16 --service-cidr=10.96.0.0/12 --ignore-preflight-errors=Swap

docker images ls (查看镜像情况)

mkdir -p $HOME/.kube

sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config

kubectl get cs (测试)
kubectl get nodes

3、部署flannel

kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml

docker inages ls
kubectl get pods -n kube-system
kubectl get ns

这节点初始化完成（）


二、node1、node2安装

master服务器：
首先要关闭防火墙，设置NTP，设置hosts解析；

从master上scp docker-ce.repo  kubenetes.repo 到node上
scp docker-ce.repo kubenetes.repo node1:/etc/yum.repos.d/
scp docker-ce.repo kubenetes.repo node2:/etc/yum.repos.d/
yum install docker-ce kubelet kubeadm

从master上scp docker.service 到node上
scp /usr/lib/systemd/system/docker.service node01:/usr/lib/systemd/system/docker.service
scp /usr/lib/systemd/system/docker.service node02:/usr/lib/systemd/system/docker.service

从master上scp kubelet 到node上
scp /etc/sysconfig/kubelet node01:/etc/sysconfig/
scp /etc/sysconfig/kubelet node01:/etc/sysconfig/

systemctl start docker
docker info
systemctl enable docker kubelet

加入集群

kubeadm join 172.20.0.70:6443 --token xxxxxxxx --discovery-token-ca-cert-hash sha256:xxxxx

docker images ls

在master节点上验证

kubectl get pods -n kube-system
kubectl get pods -n kube-system -o wide
kubectl get nodes

部署完成






