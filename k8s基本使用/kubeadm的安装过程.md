> - **安装kubeadm之前的准备工作**



> 虚拟机最少2G的内存
>
> 2Core的CPU或更多
>
> 所有虚拟机之间内网互通，外网可连
>
> 基于redhat系统搭建k8s集群
>
> 每虚拟机或主机需要有独立的主机名，MAC地址及uuid

```shell
#设置主机名,追加至主机名的那个配置文件
echo "master" > /etc/hostname

#更改主机名配置生效
hostname master

#查看主机的mac地址
iplink 
#查看主机的uuid，以下两种方式都可以查看uuid
cat /sys/class/dmi/id/produc_uuid 
dmidecode -s system-uuid   

```



> 确保某些端口是打开的：6443

```shell
#检查端口是否启动正常
nc 127.0.0.1 6443 
```



> 禁用swap功能，可以让kubectl 在集群中更好地工作

```shell
#禁用swap功能
sed -ri '/^[^#]*swap/s@^@#@' /etc/fstab

```



---



> - **kubeadm kubelet  kubectl的作用**
>
>   kubeadm：集群引导安装命令
>
>   kubelet：安装在集群中所有节点中，用来启动pods和容器，客户端的管理工具



> - **配置kubeadm安装的yum源**



```shell
cat > /etc/yum.repos.d/kubernetes.repo <<EOF
baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64/
gpgkey=https://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg

```

> - 设置selinux为permissive 模式

```shell
#设置临时生效
sudo setenforce 0

#更改配置文件永乐生效
sudo sed -i 's/^SELINUX=enforcing$/SELINUX=permissive/' /etc/selinux/config

#通过yum的方式安装kubeadm kubelet kubectl 
sudo yum install -y kubelet kubeadm kubectl --disableexcludes=kubernetes

#设置kubelet为开机自启动
sudo systemctl enable --now kubelet
```



> - **注意**

> 将selinux设置为permissive，是为了保证让容器访问本机的文件系统
>
> 在设置下载yum源的baseurl,先查看下本机的指令集

```shell
#查看本机的指令集
uname -m 
```

> - baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64/

