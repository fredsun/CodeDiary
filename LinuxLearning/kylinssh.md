//安装
sudo apt-get install openssh
//确认ssh打开
systemctl status sshd
//打开ssh
systemctl start sshd 
//确认开机启动ssh
systemctl is-enabled sshd

//连接
ssh local@192.xxx.xx.xx  
//密码
