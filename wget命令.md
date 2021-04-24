# wget



wget：

+ 支持断点下传功能
+ 同时支持FTP和HTTP下载方式
+ 支持代理服务器
+ 设置方便简单
+ 程序小，完全免费



## 断点续传

**断点续传，只需要添加 -c 参数即可**。

当文件特别大或者网络特别慢的时候，往往一个文件还没有下载完，连接就已经被切断，此时就需要断点续传。wget的断点续传是自动的，只需要使用-c参数，例如：
```bash
wget -c http://the.url.of/incomplete/file
```

使用断点续传要求服务器支持断点续传。-t参数表示重试次数，例如需要重试100次，那么就写-t 100，如果设成-t 0，那么表示无穷次重试，直到连接成功。-T参数表示超时等待时间，例如-T 120，表示等待120秒连接不上就算超时。



## 限速下载  

限速下载，只需要添加 -limit-rate=300k 合理参数即可



## 批量下载

如果有多个文件需要下载，那么可以生成一个文件，把每个文件的URL写一行，例如生成文件download.txt，然后用命令：
```bash
wget -i download.txt
```
这样就会把download.txt里面列出的每个URL都下载下来。（如果列的是文件就下载文件，如果列的是网站，那么下载首页）



## 选择性的下载

可以指定让wget只下载一类文件，或者不下载什么文件。例如：
```bash
wget -m –reject=gif http://target.web.site/subdirectory
```
表示下载http://target.web.site/subdirectory，但是忽略gif文件。

+ –accept=LIST	可以接受的文件类型
+ –reject=LIST	拒绝接受的文件类型



## 密码和认证

wget只能处理利用用户名/密码方式限制访问的网站，可以利用两个参数：
```bash
–http-user=USER设置HTTP用户
–http-passwd=PASS设置HTTP密码
```
对于需要证书做认证的网站，就只能利用其他下载工具了，例如curl.



## 利用代理服务器进行下载

如果用户的网络需要经过代理服务器，那么可以让wget通过代理服务器进行文件的下载。此时需要在当前用户的目录下创建一个.wgetrc文件。文件中可以设置代理服务器：
```bash
http-proxy = 111.111.111.111:8080
ftp-proxy = 111.111.111.111:8080
```
分别表示http的代理服务器和ftp的代理服务器。如果代理服务器需要密码则使用：
```bash
–proxy-user=USER设置代理用户
–proxy-passwd=PASS设置代理密码
```
这两个参数。使用参数–proxy=on/off 使用或者关闭代理。



wget还有很多有用的功能，需要用户去挖掘。