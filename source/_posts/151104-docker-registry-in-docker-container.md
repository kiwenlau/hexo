title: 在Docker容器中运行Docker仓库

date: 2015-11-04 14:00:00

tags: [Docker]

---

操作系统：ubuntu 14.04

本机IP为：192.168.59.10
 
##1. 安装Docker
 
```
sudo apt-get install apt-transport-https && sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 36A1D7869245C8950F966E92D8576A8BA88D21E9 && sudo bash -c "echo deb https://get.docker.io/ubuntu docker main > /etc/apt/sources.list.d/docker.list" && sudo apt-get update && sudo apt-get install -y lxc-docker && sudo docker version
```
 
##2. 在Docker容器中运行Docker仓库
 
```
sudo docker run -p 5000:5000 -d registry
```
 
##3. 测试Docker仓库
 
可以在本机以及同一个局域网的其他机器上测试

```
curl 192.168.59.10:5000 
```
 
输出信息：

```
"\"docker-registry server\""
```
 
##4. 在本机push镜像到Dcker仓库中
 
下载busybox镜像做测试

```
sudo docker pull busybox
```

查看image：

```
sudo docker images
```

输出信息(可知busybox镜像的ID为4986bf8c1536)

```
REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
busybox             latest              4986bf8c1536        2 weeks ago         2.433 MB
```
 
修改busybox镜像的tag

```
sudo docker tag 4986bf8c1536 192.168.59.10:5000/busybox:latest
```
  
push镜像到Docker仓库中

```
sudo docker push 192.168.59.10:5000/busybox:latest 
```

此时会报错：

```
FATA[0002] Error: Invalid registry endpoint https://192.168.59.10:5000/v1/: Get https://192.168.59.10:5000/v1/_ping: EOF. If this private registry supports only HTTP or HTTPS with an unknown CA certificate, please add `--insecure-registry 192.168.59.10:5000` to the daemon's arguments. In the case of HTTPS, if you have access to the registry's CA certificate, no need for the flag; simply place the CA certificate at /etc/docker/certs.d/192.168.59.10:5000/ca.crt 
```

解决方法：根据第5步修改docker的配置
 
##5. 修改Docker配置
 
修改Docker配置文件

```
sudo vim /etc/default/docker
```
 
在文件中增加以下内容

```
DOCKER_OPTS="$DOCKER_OPTS --insecure-registry=192.168.59.10:5000"
```
 
重启docker

```
sudo service docker restart
```
 
##6. 再次在本机push镜像到Docker仓库中

运行Docker仓库

```
sudo docker run -p 5000:5000 -d registry
```

push镜像到Docker仓库中

```
sudo docker push 192.168.59.10:5000/busybox:latest 
```
 
正确输出信息：

```
The push refers to a repository [192.168.59.10:5000/busybox] (len: 1)
Sending image list
Pushing repository 192.168.59.10:5000/busybox (1 tags)
511136ea3c5a: Image successfully pushed 
df7546f9f060: Image successfully pushed 
ea13149945cb: Image successfully pushed 
4986bf8c1536: Image successfully pushed 
Pushing tag for rev [4986bf8c1536] on {http://192.168.59.10:5000/v1/repositories/busybox/tags/latest}
```

  
##7. 从Docker仓库中pull镜像

在同一个局域网内的另外一台机器进行实验, 按照第1步的方法安装Docker, 按照第5步的方法配置Docker。

 
```
sudo docker pull 192.168.59.10:5000/busybox:latest 
```

 
正确输出结果：

```
Pulling repository 192.168.59.10:5000/busybox
4986bf8c1536: Download complete 
511136ea3c5a: Download complete 
df7546f9f060: Download complete 
ea13149945cb: Download complete 
Status: Image is up to date for 192.168.59.10:5000/busybox:latest
```

***

**版权声明**

转载时请注明作者[KiwenLau](http://kiwenlau.com/)以及本文地址：

[http://kiwenlau.com/2015/11/04/151104-docker-registry-in-docker-container/](http://kiwenlau.com/2015/11/04/151104-docker-registry-in-docker-container/)
***