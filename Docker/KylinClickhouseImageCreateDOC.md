
## 构建镜像
//创建独立文件夹用来打包镜像文件(在/目录下创建，保证后续的/dev文件可以拷到/kylin-docker-fs/dev)
0. cd /
1. mkdir /kylin-docker-fs && cd /kylin-docker-fs
//创建基础目录,dev 必须要创建，不然会提示找不到/dev/null
2. mkdir usr dev
3. cp -a /usr/lib /usr/lib64 /usr/bin /usr/share /usr/sbin /usr/local usr/
//创建链接
4.	ln -s usr/lib lib
	ln -s usr/lib64 lib64
	ln -s usr/bin bin
	ln -s usr/sbin sbin
	ln -s /usr/local/python38/bin/python3.8 /usr/bin/python38
	ln -s /usr/local/python38/bin/pip3.8 /usr/bin/pip3
//配置文件(我把文件都打包了，不然会遇到无法apt install和无法安装等情况)
1. cp -a /etc /root /var /tmp /boot /cdrom /media /mnt /home /opt ./
//把/dev里面的文件全部拷到/kylin-docker-fs/dev
1. cp -a /dev/*  /kylin-docker-fs/dev                                       		    */
//裁剪部分脚本，不然会提示/proc 没有mount 
1. rm -rf etc/profile.d/flatpak.sh etc/profile.d/gawk.csh etc/profile.d/gawk.sh etc/profile.d/lang.csh etc/profile.d/lang.sh etc/profile.d/system-info.sh
//测试镜像(最好测试一下apt install安装命令，保证可以安装再进行下一步，如失败，请重复保证第5步复制文件的完整性，把能复制的都复制了)
1. chroot .    此时切换了根，可测试python38等自定义bin指令是否粘贴了过来，并且配置部分参数，如参数配置中的内容，并运行没问题直接exit,
//打包到一个单独文件夹
1. cd ..
2.  mkdir /home/kylin-docker
3.  tar -C kylin-docker-fs/ -z -c . -f /home/kylin-docker/kylin-v10-docker.tar.gz
4.  cd /home/kylin-docker
//使用apt install nano安装nano
1.  sudo nano Dockerfile
	//#写入以下内容
	FROM scratch
	ADD kylin-v10-docker.tar.gz /
	LABEL \
	    org.label-schema.schema-version="1.0" \
	    org.label-schema.name="Kylin Base Image" \
	    org.label-schema.vendor="Kylin" \
	    org.label-schema.license="GPLv2" \
	    org.label-schema.build-date="20231229" \
	    org.opencontainers.image.title="Kylin Base Image" \
	    org.opencontainers.image.vendor="Kylin" \
	    org.opencontainers.image.licenses="GPL-2.0-only" \
	    org.opencontainers.image.created="2023-12-29"

	CMD ["/bin/bash"]
//写完后依次按以下操作：
//ctrl+o,Enter,ctrl+x 保存并退出
//-----------------------------
//构建镜像
14. docker build . -t kylin-v10-docker:v1.0 //(kylin-v10-docker是想构建的名字，v1.0为标签)
//构建完成后，docker images 可以查看构建出来的镜像
via:https://blog.csdn.net/weixin_45657596/article/details/133138232
//常规运行镜像
docker run -p 8000:8000 --name kylin kylin-v10-docker:v1.0 
//只打开cmd运行镜像

## 在chroot . 后配置clickhouse和docker
### clickhouse运行日志查看
vi /var/log/clickhouse-server/clickhouse-server.log
shift + g 滑到最底
也可考虑查看
vi /var/log/clickhouse-server/clickhouse-server.err.log
journalctl -xe

### 修改clickhouse的端口
vi /etc/clickhouse-server/config.xml
修改clickhouse <listen_host>为 0.0.0.0，`::`可考虑不开启

### 修改docker的DNS
vi /etc/docker/daemon.json
添加
```
,
"DNS" : ["114.114.114.114","8.8.8.8"]
```

### 新建临时/proc
测试完记得删除 /proc，这是系统运行自动创建的进程目录, 是系统进程的映射，不需要复制进docker
mkdir /proc/self/statm 
vi statm 随便输入个字母a后:wq保存

### 测试运行
service clickhouse-server start 

## 打包镜像
docker save -o kylin-clickhouse.tar kylin-clickhouse:v1.0

## 读取本地镜像文件
docker load -i kylin-clickhouse.tar