# 620 lab server

该文档最早由何泽文师兄（github账号hedes）建立，现在由曲宇勋，孙孟轩等人维护，旨在帮助大家更好地使用人工智能与机器学习团队的服务器资源。

## 1. 服务器使用
### 1.1 [现有服务器](./server_list.md)

### 1.2 如何登陆服务器
1. 登陆软件：推荐Mobaxterm[官网的免费版下载地址](https://mobaxterm.mobatek.net/download.html)
- win10 升级至1903版并安装最新更新后可用windows应用商店中的windows terminal
- 第三方软件 [Terminus](https://github.com/Eugeny/terminus)
2. 文件下载软件：mobaxterm本身可以进行文件上传下载，这里推荐FileZilla[下载地址](https://filezilla-project.org/)
3. 登陆命令：
* shell 内登陆： `ssh 账号@ip`
* Mobaxterm内登陆：基本用法[百度经验：mobaxterm ssh使用](https://jingyan.baidu.com/article/6dad5075223635a123e36ec9.html)
* 更多使用[博客：windows 全能终端](https://www.isharebest.com/mobaxterm.htm)
4. 个人账户：
由系统管理员分配(sudo adduser 账号名; 再在ssh配置中添加白名单)
注意624的机器上各个账户的home目录有20G的空间限制，所以大家应该把 数据和自己的代码程序 放在 `/media/账户名/` 中，然后软连接到 自己的home下,以张三为例
```
mkdir /media/zhangsan/projects
ln -s /media/zhangsan/projects /home/zhangsan/
```

### 1.3 linux 基础命令
1. 基本教程(国内教程链接)[http://www.runoob.com/linux/linux-tutorial.html]
2. ssh [基本教程链接](https://www.wikihow.com/Use-SSH)
3. vim [交互式教程](https://www.openvim.com/)
### 1.4 基本配置 
1. [tmux 命令使用](./tmux.md)
2. [miniconda/anaconda 安装与配置](./python_conda.md)
3. ~/.bashrc 文件配置
* .bashrc 文件，注意它和.tmux.conf 之前都有一个点号，并且同样放在自身的home目录下
    - 参考本项目中的 bashrc，将下方增加的部分放入自己的.bashrc 中即可
* 这其中用于配置个人的系统变量
    - PATH：系统路径
    - LD_LIBRARY_PATH：动态库路径
* 对624用户来说，因为无法访问外网，所以需要配置 http_proxy等变量，同样参考本项目的bashrc即可
* vim 打开，修改，保存退出后, `source ~/.bashrc` 即完成新变量的加载
## 2. [git 使用](./git_tutorial.md)
## 3. 服务器管理（管理员须知）
如果你是某台服务器的管理员，在你的用户下输入``sudo su``即可进入管理员模式，你应该要知道以下管理细节。
### 3.1 服务器网络配置
适用于新进服务器，新进服务器要配置新IP，需要先在服务器中设置网卡信息。
- 查看网卡情况
![image](https://user-images.githubusercontent.com/56111463/162695629-3088ad95-f401-4283-86fb-179250ce0ae9.png)
    输入ifconfig可以查看当前服务器网卡情况，一台服务器会有多个网卡接口，记录一下插着网线的网卡号，如图中的ens12f0卡，有实时的RX与TX，就是插着网线的网卡，请对该网卡配置IP。
- 进入网卡配置文件
    以centOS系统为例，进入centOS系统的配置文件路径
```
cd /etc/sysconfig/network-scripts/
vi ifcfg-ens12f0
```
- 配置IP，模式楼服务器可参考以下配置
```
TYPE=Ethernet
PROXY_METHOD=none
BROWSER_ONLY=no
BOOTPROTO=static
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=ens12f0
UUID=c39a9ae4-ced5-4651-a367-6d11f0a63fa7
DEVICE=ens12f0
ONBOOT=yes
IPADDR=172.18.116.33
PREFIX=24
GATEWAY=172.18.116.254

DNS1=172.18.50.3
```
### 3.2 新用户添加
- 使用命令来添加用户 zhangsan
    管理员完成`sudo adduser zhangsan` 后续按照提示输入选好的密码
- 将 zhangsan 加入ssh白名单,以备ssh访问
    管理员完成`sudo vim /etc/ssh/sshd_config` 在内部添加 zhangsan 即可
### 3.3 重启后操作
在持久模式下运行的显卡访问起来更加高效快速，所以重启之后请开启持久模式。
```
nvidia-smi -pm 1
```
### 3.4 空间已满如何操作
首先通过df -h操作查看当前服务器的存储分布，可能满的有两个空间，一个是home目录，一个是根目录。
- 根目录满了怎么办
    根目录清理建议前往/var/crash/目录下，该目录下的文件来自于服务器崩溃时的内存备份，可以删除一些较早的文件。删除文件需要管理员权限，删除时请务必小心，切勿删库跑路。
- home目录满了怎么办
    利用管理员权限前往home目录下，输入以下命令。可以显示出home下每个成员的占用空间，请定期检查并通知占用过多的用户清理其home文件。
```
du -h --max-depth=1
```


## 4. [GPU cuda 环境搭建](./gpu_cuda.md)

