title: 配置MacBook与Virtualbox ubuntu的共享文件夹

date: 2015-11-04 12:00:00

tags: [VirtualBox]

---

##1. 开启Ubuntu虚拟机，安装Guest Additions

Devices > Insert Guest Additions CD image...

挂载

``` 
sudo mount /dev/cdrom /media/cdrom
```
 
安装 

```
sudo /media/cdrom/VBoxLinuxAdditions.run
```
 
##2. 开启Ubuntu虚拟机，设置共享文件夹
 
先在mac中创建共享文件夹ubuntu
 
Devices>Shared Fold settings>+>选中文件夹ubuntu>

选中Auto-mount和Make Permanent
 
##3. 关闭Ubuntu虚拟机，在MacBook中修改共享文件夹权限

若不修改权限则将无法创建软链接。

```
VBoxManage setextradata VM_NAME VBoxInternal2/SharedFoldersEnableSymlinksCreate/SHARE_NAME 1
```
VM_NAME是虚拟机的名称, SHARE_NAME是共享文件夹名字
 
##4. 开启Ubuntu虚拟机，创建共享文件夹的软链接
 
共享文件夹位于/media目录内以sf_ 为前缀

```
ln -s /media/sf_ubuntu mac
```

***
**版权声明**
转载时请注明作者[KiwenLau](http://kiwenlau.com/)以及本文地址：[http://kiwenlau.com/2015/11/04/151104-macbook-virtualbox-ubuntu-share-fold/](http://kiwenlau.com/2015/11/04/151104-macbook-virtualbox-ubuntu-share-fold/)
***