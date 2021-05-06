# Linux lsmod命令



Linux内核具有模块化设计。 内核模块通常称为驱动程序是一段扩展内核功能的代码。 模块要么编译为可加载模块，要么内置在内核中。 可加载模块可以根据需要在正在运行的内核中进行加载和卸载，而无需重新启动系统。

通常，模块是由udev（设备管理器）按需加载的。 您也可以使用modprobe命令将模块手动加载到内核中，或者在启动时使用/etc/modules或/etc/modules-load.d/*.conf文件自动将模块加载到内核中。

内核模块存储在/lib/modules/<kernel_version>目录中。




Linux lsmod（英文全拼：list modules）命令用于显示已载入系统的模块。

执行 lsmod 指令，会列出所有已载入系统的模块。Linux 操作系统的核心具有模块化的特性，因此在编译核心时，务须把全部的功能都放入核心。您可以将这些功能编译成一个个单独的模块，待需要时再分别载入。


```bash
# lsmod 
Module         Size Used by
nfsd         238935 11 
lockd         64849 1 nfsd
nfs_acl         2245 1 nfsd
auth_rpcgss      33735 1 nfsd
sunrpc        193181 10 nfsd,lockd,nfs_acl,auth_rpcgss
exportfs        3437 1 nfsd
xt_TCPMSS        2931 1 
xt_tcpmss        1197 1 
xt_tcpudp        2011 1 
iptable_mangle     2771 1 
ip_tables        9991 1 iptable_mangle
x_tables        14299 4 xt_TCPMSS,xt_tcpmss,xt_tcpudp,ip_tables
pppoe          8943 2 
pppox          2074 1 pppoe
binfmt_misc       6587 1 
snd_ens1371      18814 0 
gameport        9089 1 snd_ens1371
snd_ac97_codec    100646 1 snd_ens1371
ac97_bus        1002 1 snd_ac97_codec
snd_pcm_oss      35308 0 
snd_mixer_oss     13746 1 snd_pcm_oss
snd_pcm        70662 3 snd_ens1371,snd_ac97_codec,snd_pcm_oss
snd_seq_dummy      1338 0 
snd_seq_oss      26726 0 
snd_seq_midi      4557 0 
snd_rawmidi      19056 2 snd_ens1371,snd_seq_midi
snd_seq_midi_event   6003 2 snd_seq_oss,snd_seq_midi
snd_seq        47263 6 snd_seq_dummy,snd_seq_oss,snd_seq_midi,snd_seq_midi_event
snd_timer       19098 2 snd_pcm,snd_seq
snd_seq_device     5700 5 snd_seq_dummy,snd_seq_oss,snd_seq_midi,snd_rawmidi,snd_seq
fbcon         35102 71 
tileblit        2031 1 fbcon
font          7557 1 fbcon
bitblit         4707 1 fbcon
ppdev          5259 0 
softcursor       1189 1 bitblit
snd          54148 10 snd_ens1371,snd_ac97_codec,snd_pcm_oss,snd_mixer_oss,snd_pcm,snd_seq_oss,snd_rawmidi,snd_seq,snd_timer,snd_seq_device
psmouse        63245 0 
serio_raw        3978 0 
soundcore        6620 1 snd
parport_pc       25962 1 
snd_page_alloc     7076 1 snd_pcm
vga16fb        11385 1 
intel_agp       24177 1 
vgastate        8961 1 vga16fb
i2c_piix4        8335 0 
shpchp         28820 0 
agpgart        31724 1 intel_agp
lp           7028 0 
parport        32635 3 ppdev,parport_pc,lp
mptspi         14652 2 
mptscsih        31325 1 mptspi
pcnet32        28890 0 
floppy         53016 0 
mii           4381 1 pcnet32
mptbase        83022 2 mptspi,mptscsih
scsi_transport_spi   21096 1 mptspi
```

说明：
+ 第1列：表示模块的名称。
+ 第2列：表示模块的大小。
+ 第3列：表示该模块调用其他模块的个数
+ 第4列：显示该模块被其他什么模块调




## modinfo-查看内核模块信息


modinfo会显示kernel模块的对象文件，以显示该模块的相关信息。

modinfo列出Linux内核中命令行指定的模块的信息。若模块名不是一个文件名，则会在/lib/modules/version 目录中搜索，就像modprobe一样。

modinfo默认情况下，为了便于阅读，以下面的格式列出模块的每个属性：fieldname : value。

参数：
```bash
  -a或--author 　显示模块开发人员。 
  -d或--description 　显示模块的说明。 
  -h或--help 　显示modinfo的参数使用方法。 
  -p或--parameters 　显示模块所支持的参数。 
  -V或--version 　显示版本信息。
```

```bash
[root@lvs001 modprobe.d]# modinfo ip_vs
filename:       /lib/modules/2.6.32-696.el6.x86_64/kernel/net/netfilter/ipvs/ip_vs.ko
license:        GPL
srcversion:     0FB85919D62C4255E412E5C
depends:        ipv6,libcrc32c
vermagic:       2.6.32-696.el6.x86_64 SMP mod_unload modversions 
parm:           conn_tab_bits:Set connections' hash size (int)
```
注意，使用lsmod不能看到内核的相关参数配置，而使用modinfo命令则可以显示



## rmmod-卸载内核模块

rmmod命令 用于从当前运行的内核中移除指定的内核模块。

执行rmmod指令，可删除不需要的模块。

选项信息：
```bash
-v：显示指令执行的详细信息；
-f：强制移除模块，使用此选项比较危险；
-w：等待着，直到模块能够被除时在移除模块；
-s：向系统日志（syslog）发送错误信息。
```

```bash
[root@lvs001 modprobe.d]# rmmod ip_vs
ERROR: Module ip_vs is in use by ip_vs_rr
```
使用rmmod卸载模块的时候，提示信息会比使用modprobe -r 的输出更详细，此时会显示该模块的被调用情况




## insmod-载入内核模块


insmod(install module)命令用于载入模块。

Linux有许多功能是通过模块的方式，在需要时才载入kernel。如此可使kernel较为精简，进而提高效率，以及保有较大的弹性。这类可载入的模块，通常是设备驱动程序。

语法:
```bash
insmod [-fkmpsvxX][-o <模块名称>][模块文件][符号名称 = 符号值]
```

参数说明：
```bash
-f 　不检查目前kernel版本与模块编译时的kernel版本是否一致，强制将模块载入。
-k 　将模块设置为自动卸除。
-m 　输出模块的载入信息。
-o<模块名称> 　指定模块的名称，可使用模块文件的文件名。
-p 　测试模块是否能正确地载入kernel。
-s 　将所有信息记录在系统记录文件中。
-v 　执行时显示详细的信息。
-x 　不要汇出模块的外部符号。
-X 　汇出模块所有的外部符号，此为预设置。
```
在Linux中，modprobe和insmod都可以用来加载module，不过现在一般都推荐使用modprobe而不是insmod了。