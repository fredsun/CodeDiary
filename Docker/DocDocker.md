Docker学习 
	分为docker和docker-desktop两种,desktop需要kvm，跳过，用compose
	镜像
	容器
1、服务
	systemctl start docker		启动docker	
	systemctl stop docker		关闭docker	
	systemctl restart docker	重启docker	
	systemctl enable docker		docker设置随服务启动而自启动	
	systemctl status docker		查看docker 运行状态				
	docker version				查看docker 版本号信息	
	docker info
2、镜像
	docker build . -t name:v1.0   已有dockerfile时，进入dockerfile所在目录执行 生成1.0版本
	docker build -t name path
	docker images				查看自己服务器中docker 镜像列表
	docker search 镜像名		搜索镜像
	docker search --filter=STARS=9000 mysql 	搜索 STARS >9000的 mysql 镜像
	docker pull 镜像名 			拉取镜像
	docker pull 镜像名:tag		拉取镜像
	docker run 镜像名			运行镜像
	docker run 镜像名:Tag
	docker rmi -f 镜像名/镜像ID		删除一个
	docker rmi -f 镜像名/镜像ID 镜像名/镜像ID 镜像名/镜像ID
						删除多个 其镜像ID或镜像用用空格隔开即可 
	docker rmi -f $(docker images -aq)
					#删除全部镜像  -a 意思为显示全部, -q 意思为只显示ID
	docker image rm 镜像名称/镜像ID			#强制删除镜像
	docker save 镜像名/镜像ID -o 镜像保存在哪个位置与名字
					#保存镜像
	docker load -i 镜像保存文件位置		加载镜像
	docker tag 源镜像名:TAG 想要生成新的镜像名:新的TAG	
				镜像标签
3、容器
	docker ps		查看正在运行容器列表
	docker ps -a	查看所有容器 -----包含正在运行 和已停止的
	docker run -it -d --name 要取的别名 镜像名:Tag /bin/bash 
					运行一个容器
	docker rm -f 容器名/容器ID	#删除一个容器
	docker rm -f 容器名/容器ID 容器名/容器ID 容器名/容器ID
		#删除多个容器 空格隔开要删除的容器名或容器ID
	docker rm -f $(docker ps -aq)		#删除全部容器
	docker exec -it 容器名/容器ID /bin/bash	进入容器1
	docker attach 容器名/容器ID				进入容器2
	#-----直接退出  未添加 -d(持久化运行容器) 时 执行此参数 容器会被关闭  
	exit
	# 优雅退出 --- 无论是否添加-d 参数 执行此命令容器都不会被关闭
	Ctrl + p + q
	docker stop 容器ID/容器名		停止容器
	docker restart 容器ID/容器名		重启容器
	docker start 容器ID/容器名		启动容器
	docker kill 容器ID/容器名		kill 容器
	docker cp 容器ID/名称: 容器内路径  容器外路径		#从容器内 拷出
	docker  cp 容器外路径 容器ID/名称: 容器内路径	#从外部 拷贝文件到容器内
	docker logs -f --tail=要查看末尾多少行 默认all 容器ID
		查看容器日志
	#修改容器配置
	docker  update --restart=always 容器Id 或者 容器名
	docker container update --restart=always 容器Id 或者 容器名
	docker rename 容器ID/容器名 新容器名	容器改名
4、例子
	端口映射例子 
	docker run -itd --name redis002 -p 8888:6379 redis:5.0.5 /bin/bash   #itd是打开一个伪终端进行交互

	跟随Docker启动
	docker run -itd --name redis002 -p 8888:6379 --restart=always  redis:5.0.5 /bin/bash

	挂载例子
	docker run -d -p 6379:6379 --name redis505 --restart=always  -v /var/lib/redis/data/:/data -v /var/lib/redis/conf/:/usr/local/etc/redis/redis.conf  redis:5.0.5 --requirepass "password"


5、docker 运维命令
	可能有时候发布会遇到如下错误:

	docker: write /var/lib/docker/tmp/GetImageBlob325372670: no space left on device

	这个错误是docker在写入的时候报错无机器无空间

	image-20220120111317635

	查看docker工作目录

	sudo docker info | grep "Docker Root Dir"
	image-20220120105454769

	查看docker磁盘占用总体情况

	du -hs /var/lib/docker/ 
	查看Docker的磁盘使用具体情况

	docker system df
	image-20220120105618983

	删除 无用的容器和 镜像

	#  删除异常停止的容器
	docker rm `docker ps -a | grep Exited | awk '{print $1}'` 
	 
	#  删除名称或标签为none的镜像
	docker rmi -f  `docker images | grep '<none>' | awk '{print $3}'`
	清除所有无容器使用的镜像

	注意，此命令只要是镜像无容器使用（容器正常运行）都会被删除，包括容器临时停止

	docker system prune -a
	查找大文件

	find / -type f -size +100M -print0 | xargs -0 du -h | sort -nr
	image-20220120112409172

	查找指定docker使用目录下大于指定大小文件

	find / -type f -size +100M -print0 | xargs -0 du -h | sort -nr |grep '/var/lib/docker/overlay2/*'
	ex：我这里是查找 /var/lib/docker/overlay2/* 开头的且大于100m的文件
	docker run
	