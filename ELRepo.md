# ELRepo



ELRepo是用于Enterprise Linux软件包的RPM存储库。ELRepo支持Red Hat Enterprise Linux（RHEL）及其衍生版本（Scientific Linux，CentOS等）。

ELRepo项目专注于与硬件相关的软件包，以增强你使用Enterprise Linux的体验。这包括文件系统驱动程序，图形驱动程序，网络驱动程序，声音驱动程序，网络摄像头和视频驱动程序。

虽然 ELRepo 是第三方仓库，但它有一个活跃社区和良好技术支持，并且CentOS官网wiki也已将它列为是可靠的（参见此处）。所以可以放心使用。


Elrepo 是国外的一个只对Linux操作系统的第三方免费软件资源库，支持Linux和CentOS操作系统的软件安装和升级，该网站没有被任何国外屏蔽，使用linux系统的用户可以通过该网站进行软件和驱动的安装和升级操作。



内核版本简写说明：

+ kernel-lt（lt=long-term）长期有效

+ kernel-ml（ml=mainline）主流版本



## GET STARTED

导入elrepo源：
```bash
rpm --import https://www.elrepo.org/RPM-GPG-KEY-elrepo.org
```

有关ELRepo项目使用的GPG密钥的详细信息，可以在密钥页面上找到。如果您的系统启用了安全启动，请参阅SecureBootKey页面以获取更多信息。



To install ELRepo for RHEL-8 or CentOS-8:
```bash
yum install https://www.elrepo.org/elrepo-release-8.el8.elrepo.noarch.rpm
```

To install ELRepo for RHEL-7, SL-7 or CentOS-7:
```bash
yum install https://www.elrepo.org/elrepo-release-7.el7.elrepo.noarch.rpm
```

要使用我们的镜像系统，请同时安装yum-plugin-fastestmirror。





## 查看最新升级包


```bash
$ yum --disablerepo="*" --enablerepo="elrepo-kernel" list available
#安装
$ yum -y install --enablerepo=elrepo-kernel kernel-lt kernel-lt-devel

# 查看安装之后的内核
$ rpm -qa | grep kernel

#查看默认内核
$ grubby --default-kernel


#如果默认内核不是最新的，可以使用如下命令更改
$ grubby --set-default /boot/vmlinuz-5.5.6-2.el8.elrepo.x86_64


#重启生效
$ reboot
```

