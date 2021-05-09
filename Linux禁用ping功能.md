# Linux禁用ping功能




如果希望屏蔽别人ping你的主机，则加入以下代码：
```bash
# Disable ping requests
net.ipv4.icmp_echo_ignore_all =1
```
编辑完成后，请执行以下命令使变动立即生效：
```bash
/sbin/sysctl -p
/sbin/sysctl -w net.ipv4.route.flush=1
