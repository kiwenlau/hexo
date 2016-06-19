title: 使用Vagrant快速创建虚拟机

date: 2016-06-18 10:00

tags: [Vagrant]

---

**摘要:** 手动创建虚拟机非常不方便，重装起来也很麻烦，打包成镜像的话则不易修改，很难进行版本控制，也无法移植到云端。[Vagrant](https://www.vagrantup.com/)可以将创建虚拟机的过程代码化，有效地解决了以上所提的痛点。

<!-- more -->

- 作者: [KiwenLau](http://kiwenlau.com/)
- 日期: [2016-06-18](http://kiwenlau.com/2016/06/18/160618-virtual-machine/)

本文所有操作是在MacBook上进行的，Windows上的操作大部分一致，但是可能会有一些小问题。

![](/image/160619/vagrant-vm.png)

##一. 快速入门

**1. 安装[VirtulBox](https://www.virtualbox.org/wiki/Downloads)**

**2. 安装[Vagrant](https://www.vagrantup.com/downloads.html)**

**3. 创建虚拟机**

```
mkdir vagrant-ubuntu
cd vagrant-ubuntu
vagrant box add ubuntu/trusty64
vagrant init ubuntu/trusty64
vagrant up --provider virtualbox
vagrant ssh
```

- **vagrant box add:** 下载创建虚拟机所依赖的**box**
- **vagrant init:** 生成创建虚拟机的所依赖的**Vagrantfile**
- **vagrant up:** 创建虚拟机
- **vagrant ssh:** SSH登陆虚拟机

不妨查看一下Vagrant自动生成的**Vagrantfile**, 我删除了所有注释：

```
Vagrant.configure(2) do |config|
   config.vm.box = "ubuntu/trusty64"
end
```
**Vagrantfile**的内容非常简单，仅定义虚拟机所依赖的**Box**为**ubuntu/trusty64**。**Box**相当于虚拟机所依赖的镜像文件。因此，这里创建的虚拟机是ubuntu trusty(14.04)。如果你需要创建其他Linux发行版例如Debian或者CentOS，可以在[这里](https://atlas.hashicorp.com/boxes/search)搜索对应的**Box**.

Vagrant虚拟机的默认配置:

- 用户/密码: vagrant/vagrant
- 共享目录: 主机上的vagrant-ubuntu目录与虚拟机内的/vagrant目录内容实时同步
- 内存：512MB
- CPU: 1

默认设置并不一定满足开发需求，下一小节将介绍如何进行定义配置。

##二. 自定义配置

**1. 修改Vagrantfile**

```
vim Vagrantfile
```

可以通过注释理解每个自定义配置的含义。

```
Vagrant.configure(2) do |config|

  # 设置虚拟机的Box
  config.vm.box = "ubuntu/trusty64"
  
  # 设置虚拟机的主机名
  config.vm.hostname="ubuntu"
  
  # 设置虚拟机的IP
  config.vm.network "private_network", ip: "192.168.0.2"
  
  # 设置主机与虚拟机的共享目录
  config.vm.synced_folder "~/Desktop/share", "/home/vagrant/share"

  # VirtaulBox相关配置
  config.vm.provider "virtualbox" do |v|

      # 设置虚拟机的名称
      v.name = "ubuntu"

      # 设置虚拟机的内存大小
      v.memory = 2048

      # 设置虚拟机的CPU个数
      v.cpus = 1
  end
  
  # 使用shell脚本进行软件安装和配置
  config.vm.provision "shell", inline: <<-SHELL

     # 安装Docker 1.11.0
     apt-get update
     apt-get install apt-transport-https ca-certificates
     apt-key adv --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys 58118E89F3A912897C070ADBF76221572C52609D
     echo deb https://apt.dockerproject.org/repo ubuntu-trusty main > /etc/apt/sources.list.d/docker.list
     apt-get update;
     apt-get install -y -q docker-engine=1.11.0-0~trusty
     usermod -aG docker vagrant

  SHELL

end
```

**2. 在桌面上创建share目录**

主机上的share目录将与虚拟机内的/home/vagrant/share目录内容实时同步

```
mkdir ~/Desktop/share
```

**3. 创建虚拟机**

```
vagrant destroy
vagrant up --provider virtualbox
```

**4. SSH免密码登陆**

使用**vagrant ssh**命令登陆虚拟机必须切换到Vagrantfile所在的目录，而直接使用虚拟机IP登陆虚拟机则更为方便:

```
ssh vagrant@192.168.0.2
```

此时SSH登陆需要输入虚拟机vagrant用户的密码，即**vagrant**

将主机的公钥复制到虚拟机的authorized_keys文件中即可实现SSH无密码登陆:

```
cat $HOME/.ssh/id_rsa.pub | ssh vagrant@127.0.0.1 -p 2222 'cat >> $HOME/.ssh/authorized_keys'
```

其中，2222是主机SSH登陆虚拟机的转发端口，可以通过以下命令查看:

```
vagrant ssh-config | grep Port
  Port 2222
```

此时SSH登陆虚拟机则不再需要输入密码。

**5. 关于Provision**

Vagrant中有下面一段内容：

```
# 使用shell脚本进行软件安装和配置
  config.vm.provision "shell", inline: <<-SHELL

     # 安装Docker 1.11.0
     apt-get update
     apt-get install apt-transport-https ca-certificates
     apt-key adv --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys 58118E89F3A912897C070ADBF76221572C52609D
     echo deb https://apt.dockerproject.org/repo ubuntu-trusty main > /etc/apt/sources.list.d/docker.list
     apt-get update;
     apt-get install -y -q docker-engine=1.11.0-0~trusty
     usermod -aG docker vagrant

  SHELL
```

其实就是嵌入了一段Shell脚本进行软件的安装和配置，这里我安装了[Docker](https://www.docker.com/)，当然也可以安装所需要的软件。修改此段内容之后，重新创建虚拟机需要使用**--provision**选项。

```
vagrant halt
vagrant up --provider virtualbox --provision
```

***
**版权声明**
转载时请注明作者[KiwenLau](http://kiwenlau.com/)以及本文地址：
[http://kiwenlau.com/2016/06/18/160618-virtual-machine/](http://kiwenlau.com/2016/06/18/160618-virtual-machine/)
***

