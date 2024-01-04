1. 虚拟机挂起后，无线网卡掉了
2. docker 启动容器后容易卡死，无法关闭容器，此时重启，直接卡死在kylinlogo
3. 网络断了，非重启解决
```
sudo service network-manager stop

sudo rm /var/lib/NetworkManager/NetworkManager.state

sudo service network-manager start

sudo vi /etc/NetworkManager/NetworkManager.conf

把false改成true

sudo service network-manager restart
————————————————
版权声明：本文为CSDN博主「致颜文磊」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/weixin_43518217/article/details/134555556
```

4. kylin下的docker不能使用sbin/init开启，会导致kylin宿主机重启