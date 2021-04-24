# Linux内存盘



内存磁盘是把一部分内存模拟成磁盘，可以把它当成一块高速的硬盘使用。

我们在做一些压测时，可能会由于磁盘性能的限制无法得到极限的压测结果，此时可以使用 RAM DISK 进行测试。


内存盘即RAM disk，它是将一部分内存分配出来，格式化成一个文件系统(tmpfs)，然后挂载到硬盘的一个目录下，就能像使用硬盘分区一样创建、删除文件和目录。

**大多数的Linux发行版本中，内存盘默认使用的是/dev/shm 路径，文件系统类型为tmpfs**。可以使用df命令查看：

```bash
[root@iZ2zecc5ecqwts1l9ypyp1Z ~]# df -h
Filesystem      Size  Used Avail Use% Mounted on
devtmpfs        869M     0  869M   0% /dev
tmpfs           879M     0  879M   0% /dev/shm
tmpfs           879M  548K  878M   1% /run
tmpfs           879M     0  879M   0% /sys/fs/cgroup
/dev/vda1        40G  6.6G   31G  18% /
tmpfs           176M     0  176M   0% /run/user/0
[root@iZ2zecc5ecqwts1l9ypyp1Z ~]# 
```

我们可以重新设置这个内存盘的大小，或者建立新的内存盘，以加速一些特别的应用，例如squid的缓冲，dns的缓冲文件等等。


重设内存盘大小，例如：
```bash
mount -o remount,size=3G /dev/shm
```
注意size的大小可以的单位是M\k\G。



## 优缺点

内存盘的优点：
+ 非常快;
+ 能够进行无数次读取和写入操作;


RAM disk的缺点：
+ 内存是易失性存储器，重启将丢失全部数据;
+ 内存的价格昂贵;




## 使用

```bash
#创建挂在文件夹
mkdir /tmp/ramdisk  

#创建设备名为myramdisk的10G内存盘并挂在到/tmp/ramdisk目录下,指定文件系统为tmpfs
mount -t tmpfs -o size=10g myramdisk /tmp/ramdisk

#卸载时使用
umount /tmp/ramdisk  
```



直接使用系统自带的 dd 进行写入测试。

```bash
----- RAM DISK
$ dd if=/dev/zero of=/tmp/ramdisk/test bs=1024 count=102400 conv=fdatasync

----- 普通硬盘
$ dd if=/dev/zero of=~/test bs=1024 count=102400 conv=fdatasync
```


现在，如果我将一个530.7M的视频文件复制到/tmp/ramdisk目录下，我的内存使用量会突然升高的。



卸载：
```bash
----- 卸载RAM DISK，释放内存空间
$ umount /tmp/ramdisk
----- 编辑fstab文件，设置开机启动
$ vim /etc/fstab
ramdisk /tmp/ramdisk tmpfs defaults,size=1G,x-gvfs-show 0 0
```
其中 x-gvfs-show 选项会在文件管理器中显示挂载的 RAM DISK 。



## tmpfs文件系统

tmpfs是一种虚拟内存文件系统，正如这个定义，它最大的特点就是它的存储空间在VM里面。

这里提一下VM(virtual memory)，VM是由linux内核里面的vm子系统管理，现在大多数操作系统都采用了虚拟内存管理机制。linux下面VM的大小由RM(Real Memory)和swap组成，RM的大小就是物理内存的大小，而Swap的大小是由你自己决定的。Swap是通过硬盘虚拟出来的内存空间，因此它的读写速度相对RM(Real Memory）要慢许多，我们为什么需要Swap呢？当一个进程申请一定数量的内存时，如内核的vm子系统发现没有足够的RM时，就会把RM里面的一些不常用的数据交换到Swap里面，如果需要重新使用这些数据再把它们从Swap交换到RM里面。


通过上面的说明，你该知道tmpfs使用的存储空间VM是什么了吧？前面说过VM由RM+Swap两部分组成，因此tmpfs最大的存储空间可达（The size of RM + The size of Swap）。 但是对于tmpfs本身而言，它并不知道自己使用的空间是RM还是Swap，这一切都是由内核的vm子系统管理的。


tmpfs一个优点就是它的大小是随着实际存储的容量而变化的。

由于/tmp目录是放临时文件的地方，因此我们可以使用tmpfs来加快速度。

