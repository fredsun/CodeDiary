## RDP / VNC / NOMACHINE
### RDP
微软, remote desktop protocol
麒麟无法走rdp

### VNC
对带宽有要求

### NO MACHINE
不支持suse



## rdp连接
```
#kylin安装xrdp
sudo apt-get install xrdp
#查看状态 active(running)
systemctl status xrdp
#root下，查看端口
netstat -tnpl
#查看防火墙
firewall-cmd --query-port=3389/tcp
#如果返回no，添加接口，并重启服务
firewall-cmd --permanent --add-port=3389/tcp
firewall-cmd --reload
```


### suse11安装rdp，因kylin的remmina无法通过rdp链接，放弃,此时win可通过mstc连接
```
zypper in xrdp
/etc/init.d/xrdp start
chkconfig xrdp on
suse11不存在systemctl,替换为/sbin/service
```

## vnc连接
### suse11sp4服务器安装vnc:
```
# VNC 可在一台linux启动了VNCServer后运行多个桌面，用端口区分，如xxx.xxx.xxx:5901, xxx.xxx.xxx:5902，其中缺省值:5901 作为默认桌面，是可选择性的桌面，只有开启了Yast Control-> Network Service -> Remote Administration(VNC)后，5901才会创建缺省桌面。否则 vncserver/vncserver :1 创建出的是~/.vnc/xstartup 规则里设置的桌面，~表示当前指定的用户，可以是，/root 或者 /user 
# Yast2中打开VNC缺省值
yast开启自带vnc  
# 窗口选择 Network Service -> Remote Administration(VNC), 回车进去后，tab切换光标，回车选中，然后Fn+f10选中Finish，Fn+F9退出

# 第一次启动
vncserver start
# 设置连接密码，重复确认密码
# 开启，确认桌面号是1，也可能2，3，4
vncserver :1 
返回：
New 'X' desktop is linux:1

Creating default startup script /root/.vnc/xstartup
Starting applications specified in /root/.vnc/xstartup
Log file is /root/.vnc/linux:1.log
也可能是 /home/user/.vnc/xstartup

# 本地尝试开启桌面程序
/root/.vnc/xstartup

# 关闭vnc
vncserver -kill :1   #关闭1号桌面，注意空格
# 如果是虚拟机，注意打开vmware里该虚拟机的vnc连接

# 修改启动程序为GNOME,
vi /home/user/.vnc/xstartup
vi /root/.vnc/xstartup,  务必修改 root 部分的 xstartup，这样 vnc 连进来后才可以用 su 权限打开 pycharm  

#!/bin/sh

xrdb $HOME/.Xresources
xsetroot -solid grey
xterm -geometry 80x24+10+10 -ls -title "$VNCDESKTOP Desktop" &
twm &

修改为

#!/bin/sh
 
# Start up the standard system desktop
unset SESSION_MANAGER
unset DBUS_SESSION_BUS_ADDRESS
 
/usr/bin/gnome-session
 
[ -x /etc/vnc/xstartup ] && exec /etc/vnc/xstartup
[ -r $HOME/.Xresources ] && xrdb $HOME/.Xresources
x-window-manager &

关闭vnc对应端口，重开新端口
# 修改分辨率，启动时：
vncserver -geometry 1920x1080
#查看支持的分辨率
xrandr 
```

### 客户机连接vnc
suse 直接`vncviewer`， 设置连接ip为 192.168.50.50:3
win 安装 RealVNCViewer, 192.168.50.50, 选择
或直接mstc
kylin 安装 remmina, 设置连接ip为192.168.50.50:5903
瘦客户机连接vnc，


### 开启自启动服务
1. 准备好XXX.sh脚本
2. 写入/etc/init.d/after.local
```
# 包含删除操作才会这么写
sudo /脚本地址/XXX.sh
否则尝试
su - username -c "/脚本地址/XXX.sh"
```
### 新建桌面快捷方式
1. .desktop格式 
   从/usr/share/application 中抄一个xxx.desktop，修改其中的name, exec, icon, comment, terminal, 放入/
   ```
   [Desktop Entry]
   Type=Application
   Name=GotoRecv
   Exec=xdg-open /home/data/
   ```
2. ln -s 软链接, 不建议，间歇性失效
   编写sh脚本，修改/root/.vnc/xstartup，添加执行创建脚本`exec XXXX.sh`，XXX.sh脚本内容为`ln -sf /home/XXXX.sh /root/Desktop/XX `

