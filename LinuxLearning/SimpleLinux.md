
1. 查看计算机位数
   `getconf LONG_BIT`
2. /usr = Unix System Resource 
/usr/bin 系统命令，随着系统升级而变化
/usr/local/bin 自定义命令，不会被系统命令覆盖
1. glibc 是GNU发布的libc库，即C语言库，别轻易升级和替换，除非全部做了备份/虚拟机，最底层的核，其他库都基于它。炸了就真的炸了，而且是所有包全炸
   realpath库用readlink替代
   pycharm版本过高用降低pycharm版本解决
   总之，别轻易修改glibc

2. 开机前 虚拟机设置-cd/DVD-启动时连接-取消，否则报错，无法加载vmware-tool

3. 解压缩VMwaretools时报错 Read-only file system
解压缩到系统文件系统即可，安装的/media目录是个只读系统

sudo tar -zxvf VMwareTools***.tar.gz -C **/**

开启terminal，开启sudo，拖动vmware

4. 概念补充
    rpm是一种包格式
    libzypp是一个包管理器，用于安装、更新、删除、查询软件包
    Yast和zypper是libzypp的前端，只负责安装卸载
    rpm -ivh 安装
    rpm -e libuuid1 --nodeps  
5. 日志输出
   命令a  > logA.log 量太多时也会只显示一部分
   script  output.log
   exit

1. gcc 编译器
   suse11sp4本身不存在c++编译器，`zypper se gcc`,`zypper se gcc-c++`

2. 关闭进程
   ```
   netstat -tnpl
   kill -9 XXXXX
   ```

3. 查看linux架构
cat /proc/version
uname -a
lsb_release -a

1. 查看ip
   suse11: ifconfig
   suse15 hostname -I

2. ip 一个32位的二进制数，被分割成了4个8位二进制数，4个字节。
同网段：子网掩码相同。
同网段可以直接通信。
不同网段通信：需要一个中间人，即网关

1. .so=Shared Object, .dll=dynamic-link library

2. 子网掩码：
概念：学号的组成，前段部分的班级号 + 后面的个人学号部分 = 网络位 + 主机位
表现：一段连续的1和一段连续的0，不连续的比如217，不能用作子网掩码。
用于判断ip是否在同一网段，子网掩码为1的位表示网络号，为0的位表示主机号。
小范围组网的子网掩码常规是255.255.255.0，但是也有例外的apple：255.255.255.240
/26 = 255.255.255.192 ，即前3个字节是1，最后1个字节的8位是 2个1，6个0，一共是 3x8+2 个1，6 个0。
11111111.11111111.11111111.11000000
/24 = 255.255.255.0，即前3个字节是1，最后1个字节的8位是 8个0，一共是 3x8 个1，8 个0。
11111111.11111111.11111111.00000000

1. 桥接网络虚拟机联网
   确认默认网关可以ping
   设置默认网关
   设置dns服务器8.8.8.8

1. kylin安装ssh
//安装
sudo apt-get install openssh-server
//确认ssh打开
systemctl status sshd
//打开ssh
systemctl start sshd 
//确认开机启动ssh
systemctl is-enabled sshd

//连接
ssh local@192.xxx.xx.xx  
//密码

如需root登录，开放rootlogin权限
修改/etc/ssh/sshd_config文件
vi /etc/ssh/sshd_config

2. suse Linux 配置ssh
参考：
https://blog.csdn.net/wenwensuper/article/details/78289376
配置ssh
主机ip： 192.168.50.34
子网掩码：255.255.255.0
默认网关：192.168.50.254
NetworkSetting 中配置 
Address：192.168.50.50
SubnetMask:/24
Hostname:oracle
HostName:oracle
DomainName:suse
NameServer:192.168.50.1 
DefaultGateway:192.168.50.254
此时应该可以ping通，但是ssh可以命令连接`ssh user@192.168.50.50`无法连接用工具链接

修改/etc/ssh/sshd_config文件
vi /etc/ssh/sshd_config

修改内容如下：
从注释里放出如下四行
Port 22
AddressFamily any
ListenAddress 0.0.0.0
ListenAddress ::
和
PasswordAuthentication no改为yes

这句暂时不需要改：
#PermitRootLogin yes   将前面的#去掉  否则提示 Permission denied, please try again.

`service sshd restart`

如果此时虚拟机还无法连接上外网，ping不通外网ip。
    1. 先ping一下网关，如果可以ping通网关。确认网关与外网设置一致
   
   验证: ssh root@xxx.xxx.xxx  
注意：ssh工具里使用的用户名是root

1. ssh命令行传递文件
```
# 传文件
scp file root@192.168.xxx.xxx:/home/data
# 传文件夹
scp -r dir root@192.168.xxx.xxx:/home/data
```


2. SUSE 
分为 openSUSE 和 Enterprise，即Linux Enterprise Server(SLES)
openSUSE分为滚动更新版本的 Tumbleweed/风滚草 和 Leap/飞跃 作为稳定版本
2014年放飞自我起了个 Leap42 作为版本号，后续回到15.x这种
目前分为 42.3 和 15.x 两个方向 
查看版本
    1. cat /proc/version //查看内核包括gcc版本
    2. uname -a  //查看内核版本
    3. lsb_release -a  //查看suse版本
    4. cat /etc/SuSE-release  //查看补丁版本

当前版本 suse11 sp4
zypper设置源：
1. 互联网源
北京交通大学源
zypper ar https://mirror.bjtu.edu.cn/opensuse/update/leap/42.3/non-oss/ update-repo-no-oss-bjtu
zypper ar https://mirror.bjtu.edu.cn/opensuse/update/leap/42.3/oss/ update-repo-oss-bjtu
zypper ar https://mirror.bjtu.edu.cn/opensuse/distribution/leap/42.3/repo/oss/ dis-repo-oss-bjtu
zypper ar https://mirror.bjtu.edu.cn/opensuse/distribution/leap/42.3/repo/non-oss/ dis-repo-non-oss-bjtu
参考: https://blog.csdn.net/rai369963/article/details/107854992
腾讯源：
https://mirrors.cloud.tencent.com/opensuse/distribution/leap/42.3/repo/non-oss/
2. 本地镜像源
mkdir /mnt/cdrom
mount /dev/sr0 /mnt/cdrom
zypper ar file:///mnt/cdrom local-source //添加本地iso镜像源, 不添加或者路径错误会导致无源, file://是文件传输协议, /mnt/cdrom 是路径, 可简写为file:/mnt/cdrom
zypper lr -UP //zypper 查看源和仓库具体地址
zypper lr //查看源 
zypper mr -d 1 2 3 4 //禁用源1234
zypper mr -e 1 2 3 4//启用源1234  mr=modifyrepo
zypper rr 1 //删除源1
zypper nr 1 //重命名源1
zypper ref //刷新源

运行pycharm
1. suse 创建 realpath脚本
   无官方realpath脚本, zypper se realpath为空，
   自定义脚本 realpath.sh
   ```
   #!/bin/sh
   readlink -f -- "$@"
   ```
   软链接到 /usr/bin/realpath, 
    sudo ln -s /homed/local/mybins/bin/realpath.sh /usr/local/bin/realpath
    如果设置错误，上一条修改后默认加 -b 覆盖

2. suse 安装 jdk
   tar -zxvf   jdk-8u131-linux-x64.tar.gz 
   cp -rf  jdk1.8.0_131  /usr/java
   vim /etc/profile
   ```
    JAVA_HOME=/usr/java/ jdk1.8.0_131
    CLASSPATH=.:$JAVA_HOME/lib/tools.jar
    PATH=$JAVA_HOME/bin:$PATH
    export JAVA_HOME CLASSPATH PATH
   ```
   source /etc/profile
    java -version

3. 运行RecvMirror.
   1. pycharm2016.3.6 OK
   2. python helloWorld OK
   3. python RecvMirror.py  can't pip
      1. 报错缺少 SNIMissingWarning，InsecurePlatformWarning
      2. 当前`openssl version` = 0.9.8
      3. 当前 python = 2.6.9 , 计划升级3.8.10

1. ln, link缩写
   1. 硬链接, 类似网盘，git，多端对同一个文件修改并上传同步。无法跨文件系统，无法指向目录，1处删除只是删除links，需删除全部才会删除inode
      1. 查看ls -l 
   2. 软链接，类似快捷方式，但可对不存在的文件进行链接
      ln -s 


2. ll, ls -l 别名。 显示文件权限，硬链接数。

3. rm -rf, 
   --r, recursive, 递归
   --f, force
4. tar, tape archive, 磁带存档$$
   --c, create, 创建归档
   --x, extract, 提取归档
   --C, directory, 中间的 -C 是指定目录, tar xxx -C XXX

5. cat a, print文件

1. kill/pkill, 杀死进程
   pkill processName
   netstat-tnpl 查看当前进程, 可能需要su权限下
   kill processID

1. 修改进入su的密码
   sudo passwd root


1. 提取deb包 
例如提取nfs-common
apt-get download nfs-common  #下载在当前目录
如需下载相关依赖包,查看下载记录
cat /var/log/apt/history.log
download其中的包

1. find
find / -name XX


1. vi复制 y, 粘贴 p, 若提示"复制了 xx行"后,按p 提示寄存器内无内容, 注意是否chroot了