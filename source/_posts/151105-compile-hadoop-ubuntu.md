title: Hadoop在Ubuntu中编译

date: 2015-11-05 14:00:00

tags: [Hadoop]

---

官网提供的编译好的hadoop-2.3.0.tar.gz二进制包是在32位系统上编译的，在64系统上运行会有一些错误，比如：

```
WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
```

此时需要自行编译hadoop 2.30 源码。本人编译的hadoop 2.30的二进制包经实验证明可以正确安装并正确运行了Word Count程序。此处可以下载我编译的二进制包: [http://pan.baidu.com/s/1eQrgsWa](http://pan.baidu.com/s/1eQrgsWa)

**更新软件列表**

```
apt-get update
```

**安装编译Hadoop所需要的软件**

```
apt-get install -y openjdk-7-jdk libprotobuf-dev protobuf-compiler maven cmake build-essential pkg-config libssl-dev zlib1g-dev llvm-gcc automake autoconf make
```

**下载hadoop 2.30的源文件包**

```
wget http://archive.apache.org/dist/hadoop/core/hadoop-2.3.0/hadoop-2.3.0-src.tar.gz 
```

**解压hadoop 2.30 的源文件包**

```
tar -xzvf hadoop-2.3.0-src.tar.gz 
```

**进入hadoop 2.30 文件夹**

```
cd hadoop-2.3.0-src
```

**编译hadoop 2.30 源文件**

```
mvn package -Pdist,native -DskipTests -Dtar
```

正确执行的结果如下:

```
[INFO] ------------------------------------------------------------------------
[INFO] Reactor Summary:
[INFO] 
[INFO] Apache Hadoop Main ................................ SUCCESS [1:11.968s]
[INFO] Apache Hadoop Project POM ......................... SUCCESS [30.393s]
[INFO] Apache Hadoop Annotations ......................... SUCCESS [18.398s]
[INFO] Apache Hadoop Assemblies .......................... SUCCESS [0.246s]
[INFO] Apache Hadoop Project Dist POM .................... SUCCESS [20.372s]
[INFO] Apache Hadoop Maven Plugins ....................... SUCCESS [23.721s]
[INFO] Apache Hadoop MiniKDC ............................. SUCCESS [1:41.836s]
[INFO] Apache Hadoop Auth ................................ SUCCESS [22.303s]
[INFO] Apache Hadoop Auth Examples ....................... SUCCESS [7.052s]
[INFO] Apache Hadoop Common .............................. SUCCESS [2:29.466s]
[INFO] Apache Hadoop NFS ................................. SUCCESS [11.604s]
[INFO] Apache Hadoop Common Project ...................... SUCCESS [0.073s]
[INFO] Apache Hadoop HDFS ................................ SUCCESS [1:30.230s]
[INFO] Apache Hadoop HttpFS .............................. SUCCESS [17.976s]
[INFO] Apache Hadoop HDFS BookKeeper Journal ............. SUCCESS [19.927s]
[INFO] Apache Hadoop HDFS-NFS ............................ SUCCESS [3.304s]
[INFO] Apache Hadoop HDFS Project ........................ SUCCESS [0.032s]
[INFO] hadoop-yarn ....................................... SUCCESS [0.033s]
[INFO] hadoop-yarn-api ................................... SUCCESS [36.284s]
[INFO] hadoop-yarn-common ................................ SUCCESS [33.912s]
[INFO] hadoop-yarn-server ................................ SUCCESS [0.213s]
[INFO] hadoop-yarn-server-common ......................... SUCCESS [8.193s]
[INFO] hadoop-yarn-server-nodemanager .................... SUCCESS [41.181s]
[INFO] hadoop-yarn-server-web-proxy ...................... SUCCESS [2.768s]
[INFO] hadoop-yarn-server-resourcemanager ................ SUCCESS [13.923s]
[INFO] hadoop-yarn-server-tests .......................... SUCCESS [0.904s]
[INFO] hadoop-yarn-client ................................ SUCCESS [4.363s]
[INFO] hadoop-yarn-applications .......................... SUCCESS [0.120s]
[INFO] hadoop-yarn-applications-distributedshell ......... SUCCESS [2.262s]
[INFO] hadoop-yarn-applications-unmanaged-am-launcher .... SUCCESS [1.615s]
[INFO] hadoop-yarn-site .................................. SUCCESS [0.086s]
[INFO] hadoop-yarn-project ............................... SUCCESS [2.703s]
[INFO] hadoop-mapreduce-client ........................... SUCCESS [0.132s]
[INFO] hadoop-mapreduce-client-core ...................... SUCCESS [18.951s]
[INFO] hadoop-mapreduce-client-common .................... SUCCESS [14.320s]
[INFO] hadoop-mapreduce-client-shuffle ................... SUCCESS [3.330s]
[INFO] hadoop-mapreduce-client-app ....................... SUCCESS [9.664s]
[INFO] hadoop-mapreduce-client-hs ........................ SUCCESS [7.678s]
[INFO] hadoop-mapreduce-client-jobclient ................. SUCCESS [9.263s]
[INFO] hadoop-mapreduce-client-hs-plugins ................ SUCCESS [1.549s]
[INFO] Apache Hadoop MapReduce Examples .................. SUCCESS [5.748s]
[INFO] hadoop-mapreduce .................................. SUCCESS [2.880s]
[INFO] Apache Hadoop MapReduce Streaming ................. SUCCESS [7.080s]
[INFO] Apache Hadoop Distributed Copy .................... SUCCESS [14.648s]
[INFO] Apache Hadoop Archives ............................ SUCCESS [2.602s]
[INFO] Apache Hadoop Rumen ............................... SUCCESS [5.706s]
[INFO] Apache Hadoop Gridmix ............................. SUCCESS [3.649s]
[INFO] Apache Hadoop Data Join ........................... SUCCESS [2.483s]
[INFO] Apache Hadoop Extras .............................. SUCCESS [2.678s]
[INFO] Apache Hadoop Pipes ............................... SUCCESS [6.359s]
[INFO] Apache Hadoop OpenStack support ................... SUCCESS [5.088s]
[INFO] Apache Hadoop Client .............................. SUCCESS [4.534s]
[INFO] Apache Hadoop Mini-Cluster ........................ SUCCESS [0.433s]
[INFO] Apache Hadoop Scheduler Load Simulator ............ SUCCESS [7.757s]
[INFO] Apache Hadoop Tools Dist .......................... SUCCESS [4.099s]
[INFO] Apache Hadoop Tools ............................... SUCCESS [0.428s]
[INFO] Apache Hadoop Distribution ........................ SUCCESS [18.045s]
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 14:59.240s
[INFO] Finished at: Thu Jan 15 18:51:59 JST 2015
[INFO] Final Memory: 168M/435M
[INFO] ------------------------------------------------------------------------
```

**编译好的二进制文件包位于**

```
hadoop-2.3.0-src/hadoop-dist/target/hadoop-2.3.0.tar.gz
```

PS: 使用自行编译的hadoop 2.30二进制包安装hadoop 2.30时需要注意删除 .bashrc文件与hadoop-env.sh文件中下面两行（默认不会有这两行，但是尝试解决报错时可能改写了）

```
export HADOOP_COMMON_LIB_NATIVE_DIR="~/hadoop/lib/"
export HADOOP_OPTS="$HADOOP_OPTS -Djava.library.path=~/hadoop/lib/"
```

***

**版权声明**

转载时请注明作者[KiwenLau](http://kiwenlau.com/)以及本文地址：

[http://kiwenlau.com/2015/11/05/151105-compile-hadoop-ubuntu/](http://kiwenlau.com/2015/11/05/151105-compile-hadoop-ubuntu/)
***