# Linux下FTP搭建



FTP是文件传输协议（File Transfer Protocal）的简写，主要完成与远程计算机的文件传输。FTP采用客户/服务器模式，客户机与服务器之间利用TCP建立连接，客户可以从服务器上下载文件，也可以把本地文件上传至服务器。


FTP服务器有**匿名**的和授权的两种。匿名的FTP服务器向公众开放，用户可以用"ftp"或"anonymous"为帐号，用电子邮箱地址为密码登录服务器；授权的FTP服务器必须用授权的账户名和密码才能登录服务器。通常匿名的用户权限较低，只能下载文件，不能上传文件。


vsftpd是一款在Linux发行版中最受推崇的FTP服务器程序。特点是小巧轻快，安全易用。vsftpd是"very secure FTP daemon"的缩写，安全性是它的一个最大的特点。


vsftpd是一个UNIX类操作系统上运行的服务器的名字，它可以运行在诸如Linux、BSD、Solaris、 HP-UNIX等系统上面，是一个完全免费的、开放源代码的ftp服务器软件，支持很多其他的FTP服务器所不支持的特征。比如：非常高的安全性需求、带宽限制、良好的可伸缩性、可创建虚拟用户、支持IPv6、速率高等。


**一定不要忘记测试匿名用户能否登录ftp服务器，防止粗心大意造成的安全隐患**。



## vsftpd的查看与安装


Linux服务器要安装ftp软件，先查看是否已经安装ftp软件下：
```bash
#which vsftpd
```


安装vsftpd
```bash
#centos
yum install vsftpd


#ubuntu
apt install vsftpd
```


输入vsftpd -v判断是否安装成功：
```bash
root@ubuntu-NF5280M5:/home# vsftpd -v
vsftpd: version 3.0.3
root@ubuntu-NF5280M5:/home# 
```

vsftpd安装完毕后，需要将vsftpd启动并设置成开机自启动：
```bash
#启动vsftpd
systemctl start vsftpd[.service]

#停止vsftpd
systemctl stop vsftpd[.service]

#重启vsftpd
systemctl restart vsftpd[.service]

#设置vsftpd开机自启动
systemctl enable vsftpd.service


或者使用service命令查看ftp服务器状态, 安装后已经启动:
service vsftpd status
```




查看vsftpd服务器状态：
```bash
root@ubuntu-NF5280M5:# service vsftpd status
● vsftpd.service - vsftpd FTP server
   Loaded: loaded (/lib/systemd/system/vsftpd.service; enabled; vendor preset: enabled)
   Active: active (running) since 四 2021-05-13 09:40:27 CST; 15min ago
 Main PID: 15478 (vsftpd)
   CGroup: /system.slice/vsftpd.service
           └─15478 /usr/sbin/vsftpd /etc/vsftpd.conf

5月 13 09:40:27 ubuntu-NF5280M5 systemd[1]: Starting vsftpd FTP server...
5月 13 09:40:27 ubuntu-NF5280M5 systemd[1]: Started vsftpd FTP server.
5月 13 09:53:48 ubuntu-NF5280M5 systemd[1]: Started vsftpd FTP server.
root@ubuntu-NF5280M5:#
```


查看服务有没有启动：【21端口】
```bash
#netstat -an | grep 21    //默认端口为21
tcp        0      0 0.0.0.0:21        0.0.0.0:*       LISTEN
```
如果看到以上信息，证明ftp服务已经开启。


定位vsftp问题时，可以使用sudo /usr/sbin/vsftpd命令启动vsftpd查看报错信息，直接使用systemctl start vsftpd无法查看报错原因！


## vsftpd的相关文件





1) vsftpd的配置文件


与绝大多数后台程序一样，刚安装好的vsftpd服务需要经过合理的配置才能使用。它的配置文件并不难找，在Debian/Ubuntu下通常是/etc/vsftpd.conf, 而RHEL/CentOS下应当是/etc/vsftpd/vsftpd.conf。


```bash
/etc/vsftpd/vsftpd.conf

#或者
/etc/vsftpd.conf
```

修改配置文件后，需要重启vsftpd。
```bash
systemctl restart vsftpd
```


2) ftpusers文件
```bash
/etc/ftpusers
```
该文件中的用户被禁止登陆，如果文件不存在在默认所有用户均允许登录. 所以确保想要登陆的账号(用户)没在这个文件内。


3) vsftpd.userlist文件

服务器可以基于用户列表文件/etc/vsftpd.userlist来允许或拒绝用户访问FTP

4) PAM认证文件
```bash
/etc/pam.d/vsftpd
```

5) 匿名用户默认目录
```bash
/var/ftp
```

6) 匿名用户的下载目录
```bash
/var/ftp/pub
```

7) FTP的上传下载日志
```bash
/var/log/xferlog
```

8) 登陆等信息
```bash
/var/log/vsftpd.log
```



## vsftpd的配置


+ ssl_enable
设置SSL加密

+ anonymous_enable

```bash
anonymous_enable=YES
```
允许匿名用户登陆FTP，为了安全起见关闭这个功能。


+ anon_upload_enable

```bash
anon_upload_enable=YES
```
是否允许匿名用户上传文件。

+ local_enable

```bash
local_enable=YES 			
```
允许使用本地用户账号登陆.

+ write_enable

```bash
write_enable=YES 			
```
允许ftp用户写数据

+ dirmessage_enable

```bash
dirmessage_enable=YES		
```
切换目录时，显示目录下.message文件中的内容，默认是开启的。


+ local_umask

```bash
local_umask=022			
```
FTP上本地的文件权限，默认是077，不过vsftpd安装后的配置文件里默认是022.没有什么特殊情况不用修改。


+ xferlog_enable

```bash
xferlog_enable=YES	
```
启用上传和下载的日志功能，默认开启。建议开启此功能，它可以对用户的操作进行日志记录，当出现问题的时候可以通过日志排查问题。

如果启用此选项，系统将会维护记录服务器上传和下载情况的日志文件，默认情况该日志文件为/var/log/vsftpd.log。



+ ftpd_banner

```bash
ftpd_banner=XXXX
```
FTP的欢迎信息。

在FTP登陆成功之后，服务器会往客户端发送一个欢迎消息以表示登陆成功。这是一个个性化的功能，您可以自由的设置其值，也可以在配置最前加上#注释本行。


+ data_connection_timeout

```bash
data_connection_timeout=120
```
数据连接超时时间。



+ 用户访问

```bash
userlist_enable=YES                     #vsftpd将会从所给的用户列表文件中加载用户名字列表
userlist_file=/etc/vsftpd.userlist      #存储用户名字的列表
userlist_deny=NO                        #允许列表中的用户访问ftp服务器
```

注意:
在默认情况下，如果通过userlist_enable=YES启用了用户列表，且设置userlist_deny=YES时，那么，用户列表文件/etc/vsftpd.userlist 中的用户是不能登录访问的。但是，选项userlist_deny=NO则反转了默认设置，这种情况下只有用户名被明确列出在/etc/vsftpd.userlist 中的用户才允许登录到FTP服务器。
另外，如果pam_service_name=vsftpd被设置，则还要查看/etc/pam.d/vsftpd的内容包含sense=deny时，/etc/pam.d/vsftpd 中列出的用户仍然不可访问ftp服务器


如果需要开启root用户的ftp权限要修改以下两个文件：
```bash
#vi /etc/vsftpd.ftpusers中注释掉root
#vi /etc/vsftpd.user_list中也注释掉root
```
然后重新启动ftp服务。



在vsftpd服务器中支持匿名用户，本地用户，和虚拟用户3类用户账号，用途及区别如下：
+ 匿名用户：是名为anonymous或ftp的用户，匿名FTP用户登录后将FTP服务器中的/var/ftp作为FTP根目录。

+ 本地用户：是 Linux 系统用户账号，使用本地用户账号登录FTP服务器后，登录目录为本地用户的宿主目录。

+ 虚拟用户：为了保证FTP服务器的安全性，由vsftpd服务器提供的非系统用户账号。虚拟用户FTP登录后将把指定的目录作为FTP根目录。虚拟用户与本地用户具有类似的功能，由于虚拟用户相对安全，因此正逐步替代本地用户账号的使用。



如何设置ftp目录呢？

**为不同用户设置不同的ftp的根目录**(配置文件中设置)：

```bash
# 用户登录路径，
#local_root针对系统用户
local_root=/var/ftp/
# 锁定用户到各自目录为其根目录
chroot_local_user=YES
# anon_root 针对匿名用户
anon_root=/var/www/html
```

修改配置文件完成。保存后重启VSFTPD。



请留意下面几处设置：
+ 如果你不希望任何人都可以登录 FTP 服务器，就应该取消 anonymous 登录权限。找到anonymous_enable这一行，设为NO.
+ 如果你期望登录FTP服务器的用户具有上传文件的功能，应添加写权限，把write_enable设为 YES.
+ 如果想通过证书而不是密码登录，需要设定rsa_cert_file 和rsa_private_key_file.
+ 修改ftpd_banner的值，当用户通过终端登录时，会显示指定的信息。





## 创建用户访问FTP


现在，你已经启动了一个正常运行的FTP服务器。凭借Linux用户名和密码登录，就可以使用FTP功能了。与SSH登录远程服务器一样，登录FTP 之后你会来到你的home目录。但是，这可能不是你所期望的，因为你必须告诉每个使用者你的Linux密码，而且你的所有文件都会暴露在光天化日之下！

如果一个团队需要在局域网使用公共的FTP服务，更好的解决办法是为FTP服务新建单独的Linux用户。


建立新的用户用以访问ftp服务器
```bash
useradd -m -c "Ciel Yang, owner of the server" -s /bin/bash ciel
passwd ciel
```
重启服务后，测试 ftp 服务器连通性(这里使用 Windows下的FileZilla FTP Client测试)

```bash
systemctl restart vsftpd
```

可以把用户加到FTP组中:
```bash
usermod -a -G ftp USERNAME
```
现在，团队就可以通过这个公共用户使用FTP服务了。


注意：**一定不要忘记测试匿名用户能否登录ftp服务器，防止粗心大意造成的安全隐患**。



## FTP工具


可以使用xftp、FileZilla等工具，排查问题时推荐FileZilla工具，会有错误提示，xftp是错误提示信息太少。



### FTP命令


连接FTP服务器：
```bash
ftp [hostname| ip-address]



```


在终端执行ftp连接的命令说明：
```bash
ftp> ascii  	#设定以ASCII方式传送文件(缺省值) 
ftp> bell   	#每完成一次文件传送,报警提示. 
ftp> binary 	#设定以二进制方式传送文件. 
ftp> case	 	#当为ON时,用MGET命令拷贝的文件名到本地机器中,全部转换为小写字母. 
ftp> cd     	#【同UNIX的CD命令】. 
ftp> cdup   	#【返回上一级目录】. 
ftp> mkdir directory-name   	#在远端主机中建立目录. 
ftp> chmod  	#改变远端主机的文件权限. 
ftp> pwd  		#【列出当前远端主机目录】. 
ftp> close  	#终止远端的FTP进程,返回到FTP命令状态, 所有的宏定义都被删除. 
ftp> delete 	#删除远端主机中的文件. 
ftp> mdelete [remote-files] 				#删除一批文件. 
ftp> dir [remote-directory] [local-file] 	#列出当前远端主机目录中的文件.如果有本地文件,就将结果写至本地文件. 
ftp> help [command] 						#输出命令的解释. 
ftp> lcd 		#改变当前本地主机的工作目录,如果缺省,就转到当前用户的HOME目录. 
ftp> ls [remote-directory] [local-file] 	#同DIR. 
ftp> macdef                 	#定义宏命令. 
ftp> open host [port] 			#【重新建立一个新的连接】. 
ftp> prompt           			#交互提示模式. 
ftp> rename [from] [to]     	#改变远端主机中的文件名. 
ftp> rmdir directory-name   	#删除远端主机中的目录. 

ftp> status   								#【显示当前FTP的状态】. 
ftp> system   								#显示远端主机系统类型. 
ftp> user user-name [password] [account] 	#重新以别的用户名登录远端主机. 
ftp> ? [command] 		#同HELP. [command]指定需要帮助的命令名称。如果没有指定command，ftp将显示全部命令的列表。
ftp> ! 					#从ftp子系统退出到外壳。 
```



关闭ftp连接的命令如下任一：
```bash
ftp> bye
ftp> exit
ftp> quit
```


下载文件：
```bash
ftp> get readme.txt 				#下载readme.txt文件
ftp> mget *.txt     				#下载 
ftp> recv remote-file [local-file] 	#同get
```


上传文件：
```bash
ftp> put /path/readme.txt 			#上传readme.txt文件
ftp> mput *.txt           			#可以上传多个文件
ftp> send local-file [remote-file] 	#同put 
```


状态码
```bash
230 - 登录成功
200 - 命令执行成功
150 - 文件状态正常，开启数据连接端口
250 - 目录切换操作完成
226 - 关闭数据连接端口，请求的文件操作成功
```







## 使用SSL/TLS保护ftp服务器


首先在/etc/ssl/下创建一个子目录来存储SSL/TLS证书和密钥文件，如果它不存在的话这样做：
```bash
mkdir /etc/ssl/private
```


在一个单一文件中生成证书和密钥，运行下面的命令：
```bash
openssl req -x509 -nodes -keyout /etc/ssl/private/vsftpd.pem -out /etc/ssl/private/vsftpd.pem -days 365 -newkey rsa:2048
```
注意：上述命令执行后会出现多行提示，要求输入个人信息以便登录方确认服务器的证书提供者信息，根据自己实际情况输入即可。（自用服务器可随意）


打开vsftpd配置文件并定义SSL详细信息：
```bash
vi /etc/vsftpd.conf
```

添加或找到选项ssl_enable，并将它的值设置为YES来激活使用SSL，同样，因为TLS比SSL更安全，启用ssl_tlsv1选项限制vsftpd只使用TLS：
```bash
ssl_enable=YES
ssl_tlsv1=YES
ssl_sslv2=NO
ssl_sslv3=NO
```

接下来，使用＃字符注释掉证书设置的行然后进行修改（或径直修改也可），如下所示：
```bash
#rsa_cert_file=/etc/ssl/private/ssl-cert-snakeoil.pem
#rsa_private_key_file=/etc/ssl/private/ssl-cert-snakeoil.key

rsa_cert_file=/etc/ssl/private/vsftpd.pem
rsa_private_key_file=/etc/ssl/private/vsftpd.pem
```
阻止匿名用户使用SSL登录，并且迫使所有的非匿名登录使用安全的SSL链接来传输数据和在登录期间发送密码：
```bash
allow_anon_ssl=NO
force_local_data_ssl=YES
force_local_logins_ssl=YES
```

提高加密算法等级：
```bash
ssl_ciphers=HIGH
```
重启服务。

```bash
systemctl restart vsftpd
```
测试ftp服务器连通性，FileZilla中选择“站点管理器”以新建站点进行测试，站点信息如下（其他信息无限更改）：
+ 协议：	FTP - 文件传输协议
+ 加密：	要求显式的FTP over TLS




## 无法登陆FTP问题


1) 问题

```bash
状态:	正在连接 192.168.34.140:21...
状态:	连接建立，等待欢迎消息...
状态:	不安全的服务器，不支持 FTP over TLS。
命令:	USER ftpuser123
响应:	331 Please specify the password.
命令:	PASS ******
响应:	530 Login incorrect.
错误:	严重错误: 无法连接到服务器
状态:	已从服务器断开
```


2) 解决办法

确保密码正确的情况下
(1) 查看/etc/ftpusers，确保账号没有在这个文件内。
(2) 修改/etc/pam.d/vsftpd
将auth required pam_shells.so修改为->auth required pam_nologin.so 即可（或者注释掉）

(3) 重启vsftpd


3) 原因
auth required pam_shells.so配置项的含义为仅允许用户的shell为/etc/shells文件内的shell命令时，才能够成功
```bash
cat /etc/shells 
# /etc/shells: valid login shells
/bin/sh
/bin/dash
/bin/bash
/bin/rbash
```
而创建ftp用户时，为了禁止ssh登录，一般多为/bin/false 、/usr/sbin/nologin 等，显然不是一个有效的bash，也就无法登录了。


## 500 OOPS问题

当我们限定了用户不能跳出其主目录之后，使用该用户登录FTP时往往会遇到这个错误：
```bash
500 OOPS: vsftpd: refusing to run with writable root inside chroot ()
```
- Add stronger checks for the configuration error of running with a writeable root directory inside a chroot(). This may bite people who carelessl     y turned on chroot_local_user but such is life.

从2.3.5之后，vsftpd增强了安全检查，如果用户被限定在了其主目录下，则该用户的主目录不能再具有写权限了！如果检查发现还有写权限，就会报该错误。

要修复这个错误，可以用命令chmod a-w /home/user去除用户主目录的写权限，注意把目录替换成你自己的。或者你可以在vsftpd的配置文件中增加下列两项中的一>项：
allow_writeable_chroot=YES

vsftp重启后一直无法重启，多次重装（apt remove）也无法重启导致郁闷半天，最后完全清除(apt --purge remove)安装才ok。