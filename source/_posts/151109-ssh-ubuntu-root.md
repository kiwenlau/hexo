title: SSH远程登陆Ubuntu的root用户

date: 2015-11-09 12:00:00

tags: [Linux]

---

##1.	关于Ubuntu的root用户

ubuntu默认是不开启root用户的。当执行管理权限的命令时，需要在命令前添加sudo，然后输入密码才能执行命令。使用passwd命令可以为root用户设置密码，使用su命令可以切换到root用户。

但是，默认情况下，不能使用SSH远程登陆Ubuntu的root用户


##2.	SSH远程登陆Ubuntu的root用户

```
sudo vim /etc/ssh/sshd_config
```

将

```
PermitRootLogin without-password
```

改为:

```
PermitRootLogin yes
```

重启SSH服务

```
sudo service ssh restart
```

这样就可以使用SSH远程登陆Ubuntu的root用户了。


***

**版权声明**

转载时请注明作者[KiwenLau](http://kiwenlau.com/)以及本文地址：

[http://kiwenlau.com/2015/11/09/151109-ssh-ubuntu-root/](http://kiwenlau.com/2015/11/09/151109-ssh-ubuntu-root/)
***