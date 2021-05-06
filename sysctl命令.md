# sysctl命令




sysctl命令，被用于在运行时**动态地修改内核的运行参数**，可用的内核参数在目录/proc/sys中。它包含一些TCP/IP堆栈和虚拟内存系统的高级选项， 这可以让有经验的管理员提高引人注目的系统性能。用sysctl可以读取设置超过五百个系统变量。


命令格式：
```bash
sysctl [-n] [-e] -w variable=value

sysctl [-n] [-e] -p <filename> (default /etc/sysctl.conf)

sysctl [-n] [-e] -a
```

命令参数：
+ -w 临时改变某个指定参数的值，如 sysctl -w net.ipv4.ip_forward=1
+ -a 显示所有的系统参数，即打印当前所有可用的内核参数变量和值
+ -A 以表格方式打印当前所有可用的内核参数变量和值
+ -p 从指定的文件加载系统参数，如不指定即从/etc/sysctl.conf中加载
+ -n 打印值时不打印关键字
+ -e 忽略未知关键字错误
+ -N 仅打印名称




列出所有的变量并查看：
```bash
sysctl -a
```



修改某变量的值：
```bash
sysctl -w 变量名=变量值

#sysctl -w vm.max_map_count=262144
```



读一个指定的变量，例如 kernel.msgmnb： 
```bash
[xt@butbueatiful ~]$ sysctl kernel.msgmnb 
kern.maxproc: 65536
```

要设置一个指定的变量，直接用 variable=value 这样的语法： 
```bash
[xt@butbueatiful ~]$ sudo sysctl kernel.msgmnb=1024
kernel.msgmnb: 1024
```
可以使用sysctl修改系统变量，也可以通过编辑sysctl.conf文件来修改系统变量。



启用IP路由转发功能：
```bash
#echo 1 > /proc/sys/net/ipv4/ip_forward

#sysctl -w net.ipv4.ip_forward=1
```

以上两种方法都可能立即开启路由功能，但如果系统重启，或执行了
```bash
# service network restart
```
命令，所设置的值即会丢失，如果想永久保留配置，可以修改/etc/sysctl.conf文件将 net.ipv4.ip_forward=0改为net.ipv4.ip_forward=1


如果希望屏蔽别人 ping 你的主机，则加入以下代码：
```bash
# Disable ping requests
net.ipv4.icmp_echo_ignore_all =1
```
编辑完成后，请执行以下命令使变动立即生效：
```bash
/sbin/sysctl -p
/sbin/sysctl -w net.ipv4.route.flush=1
```




在系统启动时，systemd-sysctl会根据以下的配置文件读取sysctl内核参数：
```bash
/etc/sysctl.d/*.conf
/run/sysctl.d/*.conf
/usr/lib/sysctl.d/*.conf
```

配置文件的格式是一系列"KEY=VALUE"行(每行一对)。空格和以"#"或";"开头的行都会被忽略。
>
> 在KEY内部，可以使用"/"或"."作为分隔符。如果第一个分隔符是"/"，那么其余的分隔符将保持原样；
> 如果第一个分隔符是"."，那么互换所有的"/"与"."；
> 例如，kernel.domainname=foo等价于kernel/domainname=foo，都会将foo写入/proc/sys/kernel/domainname参数中。
>同样的，net.ipv4.conf.enp3s0/200.forwarding等价于net/ipv4/conf/enp3s0.200/forwarding，都是指/proc/sys/net/ipv4/conf/enp3s0.200/forwarding参数。
>

配置文件依次从/etc/,/run/,/usr/lib目录中读取，配置文件的名称必须符合filename.conf格式。对于不同目录下的同名配置文件，仅以优先级最高的目录中的那一个为准。
具体说来，就是/etc/的优先级最高，/run/的优先级居中，/usr/lib的优先级最低。

