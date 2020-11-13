## 单机版kubernetes部署基本完成
>etcd，flannel，kube-apiserver,kubelet等服务能正常启动。

___

## 遇到下列问题
##### 更换docker镜像源
>sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
   "registry-mirrors":["https://m3dz4my1.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker

___

##### kubectl无法完全创建pod
使用kubectl启动container时，已经输出deployment被创建。
>kubectl run nginx --image=docker.io/nginx --replicas=1 --port=9999
deployment "nginx" created

但是查看pod时发现ready状态为0/1
>kubectl get pod

>|NAME|READY|STATUS|RESTARTS|AGE|
| ------------ | ------------ | ------------ | ------------ | ------------ |
|nginx-1847574710-9lhwp|0/1|ContainerCreating|0|3h|
|nginx2-2658713170-tp1q4|0/1|ContainerCreating|0|3h|

经度娘，说可能是kube无法访问外网的原因，导致无法下载相应的镜像。但是这里使用的是本地docker的nginx镜像。所以不是这个原因。

又有answer说是docker目录下的一个引用文件引用的文件不存在，导致deployment一直处于ContainerCreating状态。经验证该引用文件引用的文件确实是不存在，且经describe查证，错误原因确实是这样。
>kubectl describe pod nginx-1847574710-9lhwp

于是按教程的方法修改了一些docker的配置，然后docker裂开来（docker服务找不到，但是目录还在。尝试重新安装docker，装不了。希望大佬指点指点）。
尝试还原，无法还原则重装。
