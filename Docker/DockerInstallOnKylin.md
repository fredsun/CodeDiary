在 docker 官网查看最新的 docker 版本 https://download.docker.com/linux/static/stable/x86_64/
下载离线包 wget https://download.docker.com/linux/static/stable/x86_64/docker-20.10.9.tgz
解压离线包 
`tar -zxvf docker-24.0.7.tgz`
移动二进制文件 
`mv docker/* /usr/local/bin/`
测试是否成功 
`docker version`
编写 service 配置文件
```
vi /usr/lib/systemd/system/docker.service

[Unit]
Description=Docker Application Container Engine
Documentation=https://docs.docker.com
After=network-online.target firewalld.service
Wants=network-online.target
[Service]
Type=notify
ExecStart=/usr/local/bin/dockerd
ExecReload=/bin/kill -s HUP $MAINPID
LimitNOFILE=infinity
LimitNPROC=infinity
TimeoutStartSec=0
Delegate=yes
KillMode=process
Restart=on-failure
StartLimitBurst=3
StartLimitInterval=60s
[Install]
WantedBy=multi-user.target
```
刷新配置文件，
`systemctl daemon-reload`
启动 docker service，
`systemctl restart docker`
设置开机启动 
`systemctl enable docker`

安装 docker-compose
在 github 查看最新的安装包 https://github.com/docker/compose/releases
下载安装包 wget https://github.com/docker/compose/releases/download/v2.15.0/docker-compose-linux-x86_64
移动二进制文件 
`mv docker-compose-linux-x86_64 /usr/local/bin/docker-compose`
测试是否安装成功 
`docker-compose version`

作者：追风骚年
链接：https://www.jianshu.com/p/26edc7642873
来源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
