title: ssh-copy-id出错 "No such file"
date: 2015-06-20 18:19:40
tags: [SSH, Debug]
---

[ssh-copy-id](http://manpages.ubuntu.com/manpages/lucid/man1/ssh-copy-id.1.html)命令可以将本地的公钥添加到远程机器的authorized_keys中，从而实现SSH无密码登录。


![](/image/150620/ssh-copy-id.png "ssh-copy-id命令")


在使用ssh-copy-id命令时，出现了如下错误：
```sh
$ ssh-copy-id -i id_rsa.pub kiwenlau@136.187.59.2
/usr/bin/ssh-copy-id: ERROR: failed to open ID file './id_rsa': No such file
```

命令的输出信息提示没有私钥"id_rsa"文件。可知，ssh-copy-id命令执行时将检查公钥id_rsa.pub所在目录是否存在私钥id_rsa，若没有私钥则会导致命令执行失败。

这个问题的解决办法很简单，使用touch命令在公钥id_rsa.pub所在目录下创建一个空文件“id_rsa”即可。
```sh
$ touch id_rsa
```

然后ssh-copy-id命令就可以执行成功了。

```sh
$ ssh-copy-id -i id_rsa.pub kiwenlau@192.168.59.2
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'kiwenlau@192.168.59.2'"
and check to make sure that only the key(s) you wanted were added.
```

这时，使用ssh登录远程主机将不需要输入密码。
```sh
$ ssh kiwenlau@192.168.59.2
Welcome to Ubuntu 14.04.2 LTS (GNU/Linux 3.13.0-32-generic x86_64)

 * Documentation:  https://help.ubuntu.com/

  System information as of Sat Jun 20 15:39:25 JST 2015

  System load:  0.0                Processes:              93
  Usage of /:   13.0% of 17.34GB   Users logged in:        1
  Memory usage: 22%                IP address for eth0:    192.168.59.2
  Swap usage:   0%                 IP address for docker0: 172.17.42.1

  Graph this data and manage this system at:
    https://landscape.canonical.com/

22 packages can be updated.
2 updates are security updates.

Last login: Sat Jun 20 15:39:25 2015 from 192.168.59.3
```

***
**版权声明**
转载时请注明作者[KiwenLau](http://kiwenlau.com/)以及本文地址：[http://kiwenlau/2015/06/20/ssh-copy-id-error/](http://kiwenlau/2015/06/20/ssh-copy-id-error/)
***







