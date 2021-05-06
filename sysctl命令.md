# sysctl命令




sysctl命令，被用于在内核运行时**动态地修改内核的运行参数**，可用的内核参数在目录/proc/sys中。它包含一些TCP/IP堆栈和虚拟内存系统的高级选项， 这可以让有经验的管理员提高引人注目的系统性能。用sysctl可以读取设置超过五百个系统变量。


列出所有的变量并查看：
```bash
sysctl -a
```

修改某变量的值：
```bash
sysctl -w 变量名=变量值

#sysctl -w vm.max_map_count=262144
```




修改某变量的值 

sysctl -w 变量名=变量值
#sysctl -w vm.max_map_count=262144



读一个指定的变量，例如 kernel.msgmnb： 

[xt@butbueatiful ~]$ sysctl kernel.msgmnb 
kern.maxproc: 65536
要设置一个指定的变量，直接用 variable=value 这样的语法： 

[xt@butbueatiful ~]$ sudo sysctl kernel.msgmnb=1024
kernel.msgmnb: 1024
可以使用sysctl修改系统变量，也可以通过编辑sysctl.conf文件来修改系统变量。



