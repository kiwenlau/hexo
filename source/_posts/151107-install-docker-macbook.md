title: MacBook上安装Docker

date: 2015-11-07 13:00:00

tags: [Docker]

---

## 1.	下载并安装Docker for OS X Installer 

**下载地址：**

[https://github.com/boot2docker/osx-installer/releases/download/v1.4.1/Boot2Docker-1.4.1.pkg](https://github.com/boot2docker/osx-installer/releases/download/v1.4.1/Boot2Docker-1.4.1.pkg)


##2.	配置环境变量

```
vim ~/.bash_profile
```

**在.bash_profile文件中增加以下环境变量**

```
export DOCKER_CERT_PATH=/Users/nii/.boot2docker/certs/boot2docker-vm
export DOCKER_TLS_VERIFY=1
export DOCKER_HOST=tcp://192.168.59.103:2376
```

**执行以下命令使设置的环境变量生效**

```
source ~/.bash_profile
```

##3.	测试Docker

**开启boot2docker虚拟机**

```
boot2docker start
```

**运行hello-world程序**

```
docker run hello-world
```

***

**版权声明**

转载时请注明作者[KiwenLau](http://kiwenlau.com/)以及本文地址：

[http://kiwenlau.com/2015/11/07/151107-install-docker-macbook/](http://kiwenlau.com/2015/11/07/151107-install-docker-macbook/)
***