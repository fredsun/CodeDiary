# terminal
* pwd //当前绝对路径
* ls -la
* vi ~/.bash_profile  //打开配置文件
  * export JAVA_HOME=$(/usr/libexec/java_home)  //配置java_home
  * export JAVA_6_HOME=`/usr/libexec/java_home -v 1.6`
  * alias jdk6="export JAVA_HOME=$JAVA_6_HOME" //设置“jdk6”命令, 仅限于此次terminal里启动
* open .   //打开当前terminal下的finder
*

brew 主要安装commandline工具
brew cask 可以安装带GUI的应用


### mac 无法运行 adb,"zsh: command not find: adb"
原因: 从 macOS Catalina 开始, Mac 默认使用 zsh 作为默认登 Shell 和交互 Shell[Mojava] 中默认 Shell 的还是 bash.
常规的配置 bash_profile 因此失效，需要配置 zsh_profile.
解决:


### git clone 速度慢[参考自掘金:一招 git clone 加速](https://juejin.im/post/6844903862961176583)
在有梯子的情况下，修改socks5和http的端口
如图的socks5设置, 修改为
![](https://github.com/fredsun/RES/raw/16bcc68feea02ed19dcb5e308b9e0e0a82eb3a94/git_clone_slow_socks5.png)
```
# socks5协议，1080端口修改成自己的本地代理端口
git config --global http.proxy socks5://127.0.0.1:1086
git config --global https.proxy socks5://127.0.0.1:1086
```
如图的http设置，
![](https://github.com/fredsun/RES/raw/16bcc68feea02ed19dcb5e308b9e0e0a82eb3a94/git_clone_slow_http.png)
修改为
```
# http协议，1081端口修改成自己的本地代理端口
git config --global http.proxy http://127.0.0.1:1081
git config --global https.proxy https://127.0.0.1:1081
```

于是下载速度由
![](https://github.com/fredsun/RES/raw/16bcc68feea02ed19dcb5e308b9e0e0a82eb3a94/git_clone_slow_before.png)
提升到
![](https://github.com/fredsun/RES/raw/16bcc68feea02ed19dcb5e308b9e0e0a82eb3a94/git_clone_slow_after.png)
