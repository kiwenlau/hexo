title: Ubuntu静态IP配置

date: 2015-11-09 13:00:00

tags: [Linux]

---

##1. 修改Ubuntu网络配置文件

```
sudo vim /etc/network/interfaces
```

将eth0的配置改为：

```
iface eth0 inet static
address 136.187.81.160
netmask 255.255.248.0
network 136.187.80.0
broadcast 136.187.87.255
gateway 136.187.80.1
```

这些值要根据实际情况配置，本人所在的局域网范围是：

```
136.187.80.*~136.187.87.*
```

##2. 配置域名服务器

不配置域名服务器的话Ubuntu不能联网

```
sudo vim /etc/resolvconf/resolv.conf.d/base
```

增加以下内容：

```
nameserver 8.8.8.8
nameserver 8.8.4.4
```

##3. 重启Ubuntu, 查看IP

```
ifconfig
```

##4. 测试网络连接

```
ping google.com
```


***
**版权声明**
转载时请注明作者[KiwenLau](http://kiwenlau.com/)以及本文地址：
[http://kiwenlau.com/2015/11/09/151109-ubuntu-static-ip/](http://kiwenlau.com/2015/11/09/151109-ubuntu-static-ip/)
***








