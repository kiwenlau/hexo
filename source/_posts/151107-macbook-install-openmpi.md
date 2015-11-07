title: MacBook上安装OpenMPI

date: 2015-11-07 12:00:00

tags: [MPI]

---

OS X版本：10.10.1

OpenMPI版本： 1.8.4


##1. 下载源文件包

```
curl -O http://www.open-mpi.org/software/ompi/v1.8/downloads/openmpi-1.8.4.tar.gz
```

##2.	解压源文件包

```
tar zxvf openmpi-1.8.4.tar.gz
```

##3.	进入OpenMPI文件夹

```
cd openmpi-1.8.4
```

##4.	生成Makefile。

```
./configure --prefix=/usr/local
```

##5.	编译

```
MACOSX_DEPLOYMENT_TARGET=10.9 make all
```

注意，直接使用make all命令编译会产生以下错误：

```
couldn't understand kern.osversion '14.0.0
```

##6.	安装

```
sudo make install
```

##7. 测试：

**代码: mpi_hello.c**

```
#include <mpi.h>
#include <stdio.h>
 
int main (int argc, char* argv[])
{
  int rank, size;
 
  MPI_Init (&argc, &argv);      /* starts MPI */
  MPI_Comm_rank (MPI_COMM_WORLD, &rank);        /* get current process id */
  MPI_Comm_size (MPI_COMM_WORLD, &size);        /* get number of processes */
  printf( "Hello world from process %d of %d\n", rank, size );
  MPI_Finalize();
  return 0;
}
```


**编译：**

```
mpicc mpi_hello.c -o hello
```

**执行：**

```
mpirun -np 2 ./hello
```

**运行结果：**

```
Hello world from process 0 of 2
Hello world from process 1 of 2
```

***

**版权声明**

转载时请注明作者[KiwenLau](http://kiwenlau.com/)以及本文地址：

[http://kiwenlau.com/2015/11/07/151107-macbook-install-openmpi/](http://kiwenlau.com/2015/11/07/151107-macbook-install-openmpi/)
***