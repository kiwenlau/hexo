title: 为什么我们不让非root用户在CentOS, Fedora和RHEL上直接运行docker命令
date: 2015-08-15 17:07:21
tags: [Docker,翻译]
---
【编者的话】容器技术最大的弱点是安全性不足，Docker也不例外。因此，如何加强Docker的安全性是每一个Docker用户必须慎重考虑的问题。这篇文章介绍了不用sudo而直接运行docker命令所存在的安全漏洞，并强烈建议通过设置sudo规则作为暂时的解决方法。

我经常受到用户的bug报告，问我们“为什么默认情况下不能直接运行docker命令?”(译者注：默认情况下运行docker命令必须使用sudo)

Docker能够将/run/docker.socket的文件访问权限设为660, 并将其所属的用户组设为“docker”。 这将使得非root用户只要加入"docker"用户组，就可以在不使用sudo，不使用su命令切换到root用户的情况下运行docker命令。这听起来很不错。

```
ls -l /var/run/docker.sock 
srw-rw----. 1 root docker 0 Aug  3 13:02 /var/run/docker.sock
```

但是，在RHEL, Fedora和CentOS上，我们更喜欢将 doker.socket 设置为:

```
ls -l /var/run/docker.sock 
srw-rw----. 1 root root 0 Aug  3 13:02 /var/run/docker.sock
```

为什么呢？原因很简单: 如果用户可以与docker socket通信，他们就能够执行以下命令:

```
docker run -ti --privileged -v /:/host fedora chroot /host
```

这时用户将拥有主机的完全控制权。这就相当于将sudoers文件修改为以下内容(译者注：dwalsh为用户名)：

```
grep dwalsh /etc/sudoers
dwalsh  ALL=(ALL)   NOPASSWD: ALL
```

这将允许(dwalsh)用户无密码运行所有命令，获得主机的完全控制权。但是这有一个很大的安全漏洞。Docker命令没有内置的审计和日志功能，但是sudo有。

Docker目前会记录事件，但是Docker daemon重启时事件会消失。Docker目前没有审计功能。

从安全性的角度，红帽已经表达了允许非root用户在没有审计和适当的日志的情况下访问Docker daemon的顾虑。我们已经在[PR14446](https://github.com/docker/docker/pull/14446)实现了这些控制，它依靠了一个认证机制，这个机制正在讨论中。在我们实现了适当地审计和日志功能之前，我们推荐通过设置sudo规则来允许访问Docker deamon。这将允许sudo来提供审计和日志功能。

##设置sudo规则

如果你希望非root用户能够直接执行docker命令，我们推荐通过设置sudo规则来实现。下面是设置docker规则的简单教程。

在/ect/sudoers中添加以下内容: [译者注：使用visudo命令修改]
```
grep dwalsh /etc/sudoers
dwalsh        ALL=(ALL)       NOPASSWD: /usr/bin/docker
```

这将允许特定用户无需密码直接执行docker命令。

**注意**：我并不推荐使用NOPASSWD，这将使得你的系统中的任意进程获取root权限。如果你要求使用密码，则用户在运行docker命令时需要输入密码，这将使得系统稍微安全一点。如果执行命令时输入了一次密码，则sudo将允许你在5分钟内再次运行docker命令时不再需要输入密码。

然后，为docker命令设置别名。

```
alias docker="sudo /usr/bin/docker"
```

现在，非root用户将被允许直接执行doker命令(译者注：不需要使用sudo)，并且记录了日志。

```
docker run -ti --privileged -v /:/host fedora chroot /host
```

查看journal日志或者/var/log信息

```
journalctl -b | grep docker.*privileged
Aug 04 09:02:56 dhcp-10-19-62-196.boston.devel.redhat.com sudo[23422]:   dwalsh : TTY=pts/3 ; PWD=/home/dwalsh/docker/src/github.com/docker/docker ; USER=root ; COMMAND=/usr/bin/docker run -ti --privileged -v /:/host fedora chroot /host
```

查看审计日志：
```
ausearch -m USER_ROLE_CHANGE -i
type=USER_ROLE_CHANGE msg=audit(08/04/2015 09:02:56.514:1460) : pid=23423 uid=root auid=dwalsh ses=1 subj=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 msg='newrole: old-context=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023
new-context=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 exe=/usr/bin/sudo hostname=? addr=? terminal=/dev/pts/3 res=success'
```

##更强的安全性

更好的是，如果你希望只允许用户访问一个特定的容器，你可以写一个简单的脚本:

```
cat /usr/bin/docker-fedora
#!/bin/sh
docker run -ti --rm fedora /bin/sh
```

写好脚本之后，配置sudoers：

```
grep dwalsh /etc/sudoers
dwalsh        ALL=(ALL)       NOPASSWD: /usr/bin/docker-fedora
```

这个用户将仅能在没有权限限制下运行fedora容器。

##认证

我们还在开发其他程序补丁来使得docker daemon更加安全，其中包括认证方面。我们有一个正在讨论的问题[#13697“为Docker增加kerberos支持”](https://github.com/docker/docker/issues/13697)

##授权

我们还提议为Docker增加授权/RBAC(基于角色的访问控制)，来允许管理员控制哪些用户可以使用哪些容器/镜像进行哪些活动。如果你想查看这个提议或者评论或者提出建议，提议地址为[GitHub: rhatdan/docker-rbac](https://github.com/rhatdan/docker-rbac)。

##结论

我们相信在允许非root用户直接运行docker命令之前，Docker daemon的安全性还需要很多改进。在这些改进实现之前，设置sudo规则是最好的选择。我们正在开发更好的解决方案，但是暂时我们强烈推荐使用sudo。

原文链接：[Why we don't let non-root users run Docker in CentOS, Fedora, or RHEL](http://www.projectatomic.io/blog/2015/08/why-we-dont-let-non-root-users-run-docker-in-centos-fedora-or-rhel/) 

翻译链接：[为什么我们不让非root用户在CentOS, Fedora和RHEL上直接运行docker命令](http://kiwenlau.com/2015/08/15/non-root-run-docker/)

***
**版权声明**
转载时请注明作者[KiwenLau](http://kiwenlau.com/)以及本文地址：
[http://kiwenlau.com/2015/08/15/non-root-run-docker/](http://kiwenlau.com/2015/08/15/non-root-run-docker/)
***
