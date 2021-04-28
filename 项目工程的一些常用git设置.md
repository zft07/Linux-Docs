# 项目工程的一些常用git设置




配置文件
+ 全局git配置文件：~/.gitconfig

+ 单独项目git配置文件：项目目录/.git/config
  每个git项目下都会有一个隐藏的.git文件夹



## 设置用户名和邮箱


```bash
git config --global user.emali=""
git config --global user.user=""
```
这是全局的配置，所有的项目都用这个。


查看配置：
```bash
git config --list
```



## 为单个项目设置不同的用户名


比如自己公司内部的项目提交时设置的用户名为自己的真实姓名，但是在github上提交时，可能不想暴露真实姓名，这时候就不能采用通用的配置了，就要单独设置每个项目的git配置。




打开项目目录中的.git目录的config文件，添加
```bash
[user]
    name = XXX(自己的名称英文)
    email = XXXX(邮箱)
```
这就为该项目配置了独立的用户名和邮箱，这时提交代码时，提交日志上显示的就是设置的名称，当然github这种会根据设置的邮箱来设置对应的用户名。




## SSL证书问题

公司内网https的SSL证书未经过第三方机构签署，直接操作Git就会报错，需要设置忽略证书，即sslVerify。一般情况下，通过执行如下命令进行设置：
```bash
git  config  --global  http.sslVerify "false"
```
用了忽略ssl证书。

这行命令实际上是设置了当前电脑的用户的全局git配置，即所有项目如果不设置该配置，那么默认采用这个配置。实际上该行命令是修改了这个 ~/.gitconfig 这个文件。


或者打开.gitconfig文件，配置如下：
```bash
[http]
    sslVerify = false
```	
但是这些配置是相当于一个全局的配置。