<!--
 * @Author: fredsun fredsun.me@gmail.com
 * @Date: 2023-09-18 16:21:52
 * @LastEditors: fredsun fredsun.me@gmail.com
 * @LastEditTime: 2023-09-28 14:37:20
 * @FilePath: \CodeDiary\LinuxLearning\SimpleLinux.md
 * @Description: 
 * 
 * Copyright (c) 2023 by ${git_name_email}, All Rights Reserved. 
-->
1. 查看计算机位数
   `getconf LONG_BIT`
1. /usr = Unix System Resource 
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


1. 查看linux架构
cat /proc/version
uname -a
lsb_release -a

1. ip 一个32位的二进制数，被分割成了4个8位二进制数，4个字节。
同网段：子网掩码相同。
同网段可以直接通信。
不同网段通信：需要一个中间人，即网关


4. 子网掩码：
概念：学号的组成，前段部分的班级号 + 后面的个人学号部分 = 网络位 + 主机位
表现：一段连续的1和一段连续的0，不连续的比如217，不能用作子网掩码。
用于判断ip是否在同一网段，子网掩码为1的位表示网络号，为0的位表示主机号。
小范围组网的子网掩码常规是255.255.255.0，但是也有例外的apple：255.255.255.240
/26 = 255.255.255.192 ，即前3个字节是1，最后1个字节的8位是 2个1，6个0，一共是 3x8+2 个1，6 个0。
11111111.11111111.11111111.11000000
/24 = 255.255.255.0，即前3个字节是1，最后1个字节的8位是 8个0，一共是 3x8 个1，8 个0。
11111111.11111111.11111111.00000000


5. suse Linux 配置ssh
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
此时应该可以ping通，但是ssh无法连接

修改/etc/ssh/sshd_config文件
vi /etc/ssh/sshd_config

修改内容如下：

#PermitRootLogin yes   将前面的#去掉
PasswordAuthentication no改为yes
service sshd restart

如果此时虚拟机还无法连接上外网，ping不通外网ip。
    1. 先ping一下网关，如果可以ping通网关。确认网关与外网设置一致
   
6. SUSE 
分为 openSUSE 和 Enterprise，即Linux Enterprise Server(SLES)
openSUSE分为滚动更新版本的 Tumbleweed/风滚草 和 Leap/飞跃 作为稳定版本
2014年放飞自我起了个 Leap42 作为版本号，后续回到15.x这种
目前分为 42.3 和 15.x 两个方向 
zypper设置源：
北京交通大学源
zypper ar https://mirror.bjtu.edu.cn/opensuse/update/leap/42.3/non-oss/ update-repo-no-oss-bjtu
zypper ar https://mirror.bjtu.edu.cn/opensuse/update/leap/42.3/oss/ update-repo-oss-bjtu
zypper ar https://mirror.bjtu.edu.cn/opensuse/distribution/leap/42.3/repo/oss/ dis-repo-oss-bjtu
zypper ar https://mirror.bjtu.edu.cn/opensuse/distribution/leap/42.3/repo/non-oss/ dis-repo-non-oss-bjtu
参考: https://blog.csdn.net/rai369963/article/details/107854992
腾讯源：
https://mirrors.cloud.tencent.com/opensuse/distribution/leap/42.3/repo/non-oss/
zypper ar https://mirror.bjtu.edu.cn/opensuse/update/leap/42.3/non-oss/ update-repo-no-oss-bjtu

1. suse 创建 realpath脚本
   无官方realpath脚本, zypper se realpath为空，
   自定义脚本 realpath.sh
   ```
   #!/bin/sh
   readlink -f -- "$@"
   ```
   软链接到 /usr/bin/realpath, 
    sudo ln -s /home/local/mybins/bin/realpath.sh /usr/local/bin/realpath
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
      2. 当前`openssl version` =0.9.8
      3. 当前 python = 2.6.9

Collecting anyio==2.0.0
  Could not find a version that satisfies the requirement anyio==2.0.0 (from versions: )
No matching distribution found for anyio==2.0.0
/home/local/RecvMirror/venv2/lib/python2.6/site-packages/pip/_vendor/urllib3/util/ssl_.py:339: SNIMissingWarning: An HTTPS request has been made, but the SNI (Subject Name Indication) extension to TLS is not available on this platform. This may cause the server to present an incorrect TLS certificate, which can cause validation failures. You can upgrade to a newer version of Python to solve this. For more information, see https://urllib3.readthedocs.io/en/latest/advanced-usage.html#ssl-warnings
  SNIMissingWarning
/home/local/RecvMirror/venv2/lib/python2.6/site-packages/pip/_vendor/urllib3/util/ssl_.py:137: InsecurePlatformWarning: A true SSLContext object is not available. This prevents urllib3 from configuring SSL appropriately and may cause certain SSL connections to fail. You can upgrade to a newer version of Python to solve this. For more information, see https://urllib3.readthedocs.io/en/latest/advanced-usage.html#ssl-warnings
  InsecurePlatformWarning
