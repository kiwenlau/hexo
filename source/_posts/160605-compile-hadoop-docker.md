title: 基于Docker编译Hadoop

date: 2016-06-05 09:00

tags: [Docker, Hadoop]

---

**摘要:** 将编译Hadoop所需要的依赖软件安装到Docker镜像中，然后在Docker容器中编译Hadoop，可以有效提高编译效率，同时避免污染主机。

**GitHub地址:**
- [kiwenlau/compile-hadoop](https://github.com/kiwenlau/compile-hadoop)

<!-- more -->

![](/image/160605/hadoop-docker.png)

Hadoop官网提供的二进制包是在32位系统上编译的，在64系统上运行会出错：

```
WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
```

这时需要自行编译Hadoop源码，在前一篇博客中，我介绍了[64位Ubuntu中编译Hadoop的步骤](http://kiwenlau.com/2016/05/29/160529-compile-hadoop-ubuntu/)。

以下为编译步骤:

**1. 下载Docker镜像**

```
sudo docker pull kiwenlau/compile-hadoop
```

或者自行构建Docker镜像

```
sudo docker build -t kiwenlau/compile-hadoop .
```


**2. 下载并解压[Hadoop源文件](http://archive.apache.org/dist/hadoop/core/)**

```
export VERSION=2.7.2
wget http://archive.apache.org/dist/hadoop/core/hadoop-$VERSION/hadoop-$VERSION-src.tar.gz
tar -xzvf hadoop-$VERSION-src.tar.gz
```

**3. 运行Docker容器，在容器中编译Hadoop**

```
sudo docker run -v $(pwd)/hadoop-$VERSION-src:/hadoop-$VERSION-src kiwenlau/compile-hadoop /root/compile.sh $VERSION
```

这一步比较耗时，大概需要15~30分钟。 

正确执行的结果如下:

```
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 23:46.056s
[INFO] Finished at: Tue May 31 16:40:53 UTC 2016
[INFO] Final Memory: 210M/915M
[INFO] ------------------------------------------------------------------------


comile hadoop 2.7.2 success!
```

编译好的二进制文件包位于

```
hadoop-2.3.0-src/hadoop-dist/target/hadoop-2.3.0.tar.gz
```

编译其他版本的Hadoop的步骤一致，仅需改变VERSION的值。

也可以直接下载我编译好的Hadoop:

- [百度网盘](https://pan.baidu.com/s/1hrGLqlA)
- [GitHub](https://github.com/kiwenlau/compile-hadoop/releases)

Linux终端用户可以使用wget命令直接下载GitHub上的Hadoop二进制包:

```
wget https://github.com/kiwenlau/compile-hadoop/releases/download/2.3.0/hadoop-2.3.0.tar.gz
wget https://github.com/kiwenlau/compile-hadoop/releases/download/2.4.0/hadoop-2.4.0.tar.gz
wget https://github.com/kiwenlau/compile-hadoop/releases/download/2.5.0/hadoop-2.5.0.tar.gz
wget https://github.com/kiwenlau/compile-hadoop/releases/download/2.6.0/hadoop-2.6.0.tar.gz
wget https://github.com/kiwenlau/compile-hadoop/releases/download/2.7.0/hadoop-2.7.0.tar.gz
wget https://github.com/kiwenlau/compile-hadoop/releases/download/2.7.2/hadoop-2.7.2.tar.gz
```

另外, 使用自行编译的Hadoop二进制包安装Hadoop时需要删除.bashrc文件与hadoop-env.sh文件中下面两行（默认不会有这两行，但是尝试解决报错时可能改写了）

```
export HADOOP_COMMON_LIB_NATIVE_DIR="~/hadoop/lib/"
export HADOOP_OPTS="$HADOOP_OPTS -Djava.library.path=~/hadoop/lib/"
```

***
**版权声明**
转载时请注明作者[KiwenLau](http://kiwenlau.com/)以及本文地址：
[http://kiwenlau.com/2016/06/05/160605-compile-hadoop-docker/](http://kiwenlau.com/2016/06/05/160605-compile-hadoop-docker/)
***