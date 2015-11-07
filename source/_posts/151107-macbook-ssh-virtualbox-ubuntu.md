title: MacBook通过SSH登陆VirtualBox中的Ubuntu

date: 2015-11-07 14:00:00

tags: [VirtualBox]

---

##1. 虚拟机网卡配置

网卡1：NAT

网卡2：Host-only，界面名称为vboxnet0

##2. 获取vboxnet0的IP地址
	
**在MacBook的终端中执行:**

```	
ifconfig
```
	
**运行结果：**

```
vboxnet0: flags=8943<UP,BROADCAST,RUNNING,PROMISC,SIMPLEX,MULTICAST> mtu 1500
ether 0a:00:27:00:00:00 
inet 192.168.59.3 netmask 0xffffff00 broadcast 192.168.59.255
```

可知vboxnet0的IP地址为192.168.59.3
	
##3. 配置Ubuntu的eth1

```
sudo vim /etc/network/interfaces
```
	 
**增加以下内容：192.168.59.1与vboxnet0的IP地址192.168.59.3对应**

```
auto eth1
iface eth1 inet static
address 192.168.59.1
netmask 255.255.255.0
```
			 
**激活eth1**

```
sudo ifup eth1
```
	 
**重启虚拟机:**

```
sudo reboot
```
	 
##4. 在MacBook终端中通过SSH登陆虚拟机Ubuntu

```
ssh liu@192.168.59.1
```
	
***

##5. 通过配置端口转发

放弃设置端口转发的方法，因为有多个虚拟机时不方便。

在MacBook的终端设置端口转发，把本机的2222端口转发到虚拟机Ubuntu的22端口，也就是ssh的端口（虚拟机Ubuntu需要关机）

```
VBoxManage modifyvm "Ubuntu" --natpf1 "guestssh,tcp,,2222,,22"
```

在MacBook终端中通过SSH连接虚拟机Ubuntu （虚拟机Ubuntu必须开机）

```
ssh -p 2222 liu@127.0.0.1
```

***

**版权声明**

转载时请注明作者[KiwenLau](http://kiwenlau.com/)以及本文地址：

[http://kiwenlau.com/2015/11/07/151107-macbook-ssh-virtualbox-ubuntu/](http://kiwenlau.com/2015/11/07/151107-macbook-ssh-virtualbox-ubuntu/)
***