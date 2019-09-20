查看node节点详细信息

kubectl describe node node01

查看master节点详细信息

kubectl describe node master

查看版本

kubrctl version

查看集群信息

kubectl cluster-info


一、创建
kubectl run --help

kubectl run nginx-deploy --image=nginx:1.14-alpine --port=80 --replicas=1 --dry-run=true

kubectl run nginx-deploy --image=nginx:1.14-alpine --port=80 --replicas=1


二、查看

kubectl get deployment

kubectl get pods

kubectl get pods -o wide

node02网络


三、删除

kubectl delete pods nginx-deploy-xxxx-xxxx

删除后会自动创建新的pod


将资源暴露为新的Kubernetes Service

kubectl expose --help

kubectl expose deployment nginx-deploy --name=nginx --port=80 --target-port=80 --protocol=TCP

kubectl get svc

kubectl get pods -n kube-system -o wide

kubectl get svc -n kube-system


kubedns

安装 dig

yum install binf-utils

dig -t A nginx.default.svc.cluster.local @10.96.0.10


创建busybox

kubectl run client --images=busybox --replicas=1 -it --restart=Never



wget nginx

wget -O - -q http://nginx:80/


删除测试


kubectl get pods

kubectl delete pod nginx-deploy-xxxx-xxx

kubectl get pods

在busybox上访问测试

wget nginx

wget -O - -q http://nginx:80/

kubectl get pods -o wide

kubectl get svc

kubectl describe svc nginx

kubectl get pods --show-labels


四、删除svc和创建

kubectl delete svc nginx

kubectl get svc

kubectl expose deployment nginx-deploy --name=nginx

查看deployment详细

kubectl describe deployment nginx-deploy


五、创建新的2副本pod

kubectl run myapp -images=myapp:v1 --replicas=2

kubectl get deployment

kubectl get deployment -w (监控，不退出)

kubectl get deployment

kubectl get pods -o wide

kubectl expose deployment myapp --name=myapp --port=80

在busybox上访问测试

wget -O - -q 10.244.1.4

wget -O - -q 10.244.2.4

wget -O - -q 10.244.1.4/hostname.html

wget -O - -q 10.244.2.4/hostname.html

while true; do wget -o - -q myapp/hostname.html; sleep 1 ;down


六、扩展和缩减副本数量 

kubectl scale --help

kubectl scale --replicas=5 deployment myapp

kubectl get pods

kubectl scale --replicas=3 deployment myapp

kubectl get pods


七、更新升级

kubectl set images --help

kubectl set images deployment myapp myapp=myaap:v2

kubectl rollout status updated

kubectl get pods


注：pod名称已发生变化

kubectl describe pods myapp-xxxx-xx (查看镜像版本已升级)


在busybox上访问测试(升级到V2)

while true; do wget -o - -q myapp; sleep 1 ;down


八、回滚

kubectl rollout --help

kubectl rollout undo deployment myapp 

注：如不指定回滚版本，默认为回滚到上一版本

kubectl get pods

在busybox上访问测试(回退到V1)

while true; do wget -o - -q myapp; sleep 1 ;down

查看node节点iptables规则

iptables -vnL

iptables -vnL -t nat


九、修改

kubectl edit

使用默认编辑器 编辑服务器上定义的资源。

kubectl edit --help

kubectl edit 

修改：在集群外部访问nginx

kubectl edit svc myapp

type:ClusterIP 修改成 NodePort

kubectl get pods

在集群外部访问

http://172.20.0.66:30020

http://172.20.0.67:30020

防止node节点挂掉，手工在集群外部增加负载均衡机制（lvs+keeplived）

其他：

Kubernetes中文社区 | 中文文档

http://docs.kubernetes.org.cn/
