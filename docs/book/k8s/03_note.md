### Note

#### 1. kube使用默认的镜像时

kubeadm init的时, container的会引入node的环境变量, 由此可能造成一些问题
如在主节点写了 export https_proxy=http://192.168.1.51:7890;export http_proxy=http://192.168.1.51:7890;export all_proxy=socks5://192.168.1.51:7890
所有的node都会使用这些环境变量, 导致proxy代理出错

```

```

2. 运维技巧, 如何vmware克隆一个完备桥接模式的centos镜像
   ```
   需要在镜像下克隆, 然后将原有的mac地址复制到新的桥接机器上
   (猜测宿主机mac id和新机器的uuid配置文件要一致 
    /etc/sysconfig/network-scripts/ifcfg-ens33
   )

   需要重启

   systemctl stop NetworkManager                             临时关闭
   systemctl disable NetworkManager                          永久关闭网络管理命令
   systemctl start network.service                           开启网络服务

   需要重启

   ```
