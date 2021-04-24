# zlib库

zlib是一个很好的压缩解压缩库，提供了一套 in-memory 压缩和解压函数，并能检测解压出来的数据的完整性(integrity)。



网站：

+ https://zlib.net/
+ http://www.gzip.org/




查看Linux上是否已安装：

```bash
whereis zlib
```



可以进行yum安装或源码安装，yum安装：

```bash
yum install -y zlib zlib-devel
```



源码安装：

```bash
./configure
make
sudo make install
```



使用gcc编译程序时使用参数-lz来使用zlib库，zlib库的函数和使用暂略。


