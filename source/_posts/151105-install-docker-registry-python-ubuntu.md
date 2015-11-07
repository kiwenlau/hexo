title: 在Ubuntu上安装Python版Docker仓库

date: 2015-11-05 12:00:00

tags: [Docker]

---

安装Docker仓库的Ubuntu的局域网IP为: 192.168.59.10

##1.	安装Docker仓库

```
sudo apt-get update
sudo apt-get -y install build-essential python-dev libevent-dev python-pip
liblzma-dev swig libssl-dev
sudo pip install docker-registry
```

安装时会有很多warning以及少量error，暂时忽略。

##2.	测试Docker仓库是否安装成功

```
docker-registry --help
```

输出：

```
usage: docker-registry [-h]

run the docker-registry with gunicorn, honoring the following
environment variables:
REGISTRY_HOST: TCP host or ip to bind to; default is 0.0.0.0
REGISTRY_PORT: TCP port to bind to; default is 5000
GUNICORN_WORKERS: number of worker processes gunicorn should start
GUNICORN_GRACEFUL_TIMEOUT: timeout in seconds for graceful worker restart
GUNICORN_SILENT_TIMEOUT: timeout in seconds for restarting silent workers
GUNICORN_USER: unix user to downgrade priviledges to
GUNICORN_GROUP: unix group to downgrade priviledges to
GUNICORN_ACCESS_LOG_FILE: File to log access to
GUNICORN_ERROR_LOG_FILE: File to log errors to
GUNICORN_OPTS: extra options to pass to gunicorn

optional arguments:
  -h, --help  show this help message and exit
```

##3.	配置Docker仓库

```
cd /usr/local/lib/python2.7/dist-packages/docker_registry/lib/../../config/
sudo cp config_sample.yml config.yml
sudo mkdir -p /var/docker-registry
```

修改config.yml文件，将/tmp目录改为/var/docker-registry目录

```
sudo vim config.yml
```

将：

```
# SQLite search backend
    sqlalchemy_index_database: _env:SQLALCHEMY_INDEX_DATABASE:sqlite:////tmp/docker-registry.db	
```
			
改为：

```
# SQLite search backend
sqlalchemy_index_database: _env:SQLALCHEMY_INDEX_DATABASE:sqlite:////var/docker-registry/docker-registry.db
```

将：

```
local: &local
    <<: *common
    storage: local
    storage_path: _env:STORAGE_PATH:/tmp/registry
```

改为

```
local: &local
    <<: *common
    storage: local
    storage_path: _env:STORAGE_PATH:/var/docker-registry/registry
```


##4. 测试配置是否正确

```
gunicorn --access-logfile - --debug -k gevent -b 0.0.0.0:5000 -w 1 docker_registry.wsgi:application 
```

输出为以下内容说明应该没错…(虽然有warning)

```
20/Jan/2015:20:46:27 +0000 WARNING: Cache storage disabled!
20/Jan/2015:20:46:27 +0000 WARNING: LRU cache disabled!
20/Jan/2015:20:46:27 +0000 DEBUG: Will return docker-registry.drivers.file.Storage
```


##5.	继续配置

```
sudo mkdir -p /var/log/docker-registry
sudo vim /etc/init/docker-registry.conf
```

在docker-registry.conf文件中写入以下内容：(此处0.0.0.0:5000很关键, 若设为localhost:5000则无法将镜像pull到192.168.59.10: 5000)

```
description "Docker Registry"

start on runlevel [2345]
stop on runlevel [016]

respawn
respawn limit 10 5

script
exec gunicorn --access-logfile /var/log/docker-registry/access.log --error-logfile /var/log/docker-registry/server.log -k gevent --max-requests 100 --graceful-timeout 3600 -t 3600 -b 0.0.0.0:5000 -w 8 docker_registry.wsgi:application
end script
```


##6.	启动Dcoker仓库

```
sudo service docker-registry start
```

测试Docker仓库

```
curl 192.168.59.10:5000
```
 
正确输出为(此处若出错应该是docker-registry.conf文件中0.0.0.0:5000设为了localhost:5000)

```
"\"docker-registry server\""
```


##7. 安装Docker

首先在本机(Ubuntu 14.04)上进行测试，需要安装Docker

```
sudo apt-get install apt-transport-https
sudo apt-key adv --keyserver hkp://keyserver.Ubuntu.com:80 --recv-keys 36A1D7869245C8950F966E92D8576A8BA88D21E9
sudo bash -c "echo deb https://get.docker.io/Ubuntu docker main > /etc/apt/sources.list.d/docker.list"
sudo apt-get update
sudo apt-get install -y lxc-docker
```

查看docker版本信息

```
sudo docker version
```

输出:

```
Client version: 1.4.1
Client API version: 1.16
Go version (client): go1.3.3
Git commit (client): 5bc2ff8
OS/Arch (client): linux/amd64
Server version: 1.4.1
Server API version: 1.16
Go version (server): go1.3.3
Git commit (server): 5bc2ff8
```


##8.	配置Docker

这一步不做push和pull将会出错

```
sudo vim /etc/default/docker
```

增加以下一行：

```
DOCKER_OPTS="$DOCKER_OPTS --insecure-registry=192.168.59.10:5000"
```

重启Docker

```
sudo service docker restart
```

##9. push镜像到Docker仓库中

下载busybox镜像作为测试

```
sudo docker pull busybox
```

查看image

```
sudo docker images
```

busybox镜像的ID为4986bf8c1536

```
REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
busybox             latest              4986bf8c1536        2 weeks ago         2.433 MB
```

改变busybox镜像的tag

```
sudo docker tag 4986bf8c1536 192.168.59.10:5000/busybox:test
```

将busybox镜像push到本机的Docker仓库中

```
sudo docker push 192.168.59.10:5000/busybox:test
```

输出：

```
The push refers to a repository [192.168.59.10:5000/busybox] (len: 1)
Sending image list
Pushing repository 192.168.59.10:5000/busybox (1 tags)
511136ea3c5a: Image successfully pushed 
df7546f9f060: Image successfully pushed 
ea13149945cb: Image successfully pushed 
4986bf8c1536: Image successfully pushed 
Pushing tag for rev [4986bf8c1536] on {http://192.168.59.10:5000/v1/repositories/busybox/tags/test}
```


##10. 从Docker仓库中pull镜像

在局域网的另一台机器上安装并配置好Docker后，即可pull刚才push过的image

```
sudo docker pull 192.168.59.10:5000/busybox:test
```

正确输出结果为：

```
Pulling repository 192.168.59.10:5000/busybox
4986bf8c1536: Download complete 
511136ea3c5a: Download complete 
df7546f9f060: Download complete 
ea13149945cb: Download complete 
Status: Downloaded newer image for 192.168.59.10:5000/busybox:test
```

***

**版权声明**

转载时请注明作者[KiwenLau](http://kiwenlau.com/)以及本文地址：

[http://kiwenlau.com/2015/11/05/151105-install-docker-registry-python-ubuntu/](http://kiwenlau.com/2015/11/05/151105-install-docker-registry-python-ubuntu/)
***