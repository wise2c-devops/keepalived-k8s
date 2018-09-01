# Keepalived for Kubernetes cluster setup
Keepalived for Kubernetes cluster setup

使用方法：

编辑start-keepalived.sh文件，修改虚拟IP地址VIRTUAL_IP、虚拟网卡设备名INTERFACE、虚拟网卡的子网掩码NETMASK_BIT、路由标识符RID、虚拟路由标识符VRID的值为实际Kubernetes集群所使用的值：

（CHECK_PORT的值6444一般不用修改，它是HAProxy的暴露端口，内部指向Kubernetes Master Server的6443端口）

例如：
```
#!/bin/bash
VIRTUAL_IP=192.168.9.30
INTERFACE=ens33
NETMASK_BIT=24
CHECK_PORT=6444
RID=10
VRID=160
MCAST_GROUP=224.0.0.18
```

运行start-haproxy.sh脚本
```
bash start-keepalived.sh
```

附注：为避免与局域网内其它使用Keepalived虚拟IP的应用产生冲突，请先用tcpdump抓包工具探测一下局域网内所使用的RID及VRID值，命令如下：

```
tcpdump -nn -i any net 224.0.0.0/8
```

扫描结果范例如下：

```
[root@k8s01 ~]# tcpdump -nn -i any net 224.0.0.0/8
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on any, link-type LINUX_SLL (Linux cooked), capture size 262144 bytes
18:45:39.823877 IP 192.168.9.11 > 224.0.0.18: VRRPv2, Advertisement, vrid 160, prio 100, authtype simple, intvl 1s, length 20
18:45:40.824667 IP 192.168.9.11 > 224.0.0.18: VRRPv2, Advertisement, vrid 160, prio 100, authtype simple, intvl 1s, length 20
18:45:41.826097 IP 192.168.9.11 > 224.0.0.18: VRRPv2, Advertisement, vrid 160, prio 100, authtype simple, intvl 1s, length 20
18:45:42.826900 IP 192.168.9.11 > 224.0.0.18: VRRPv2, Advertisement, vrid 160, prio 100, authtype simple, intvl 1s, length 20
```

这就说明不能再使用VRID=160的设定了。
