尝试给 一加7pro 安装了Magisk中的Riru-Core + Riru-EdXposed + 太极(阳)模块和安装太极app，导致手机卡在“手机正在启动”，[发生同一情况的链接](https://www.oneplusbbs.com/forum.php?mod=viewthread&tid=4797309)
此时重复关机打开依然是“手机正在启动”，正确的处理方式应该是[链接](http://www.oneplusbbs.com/thread-5024341-1.html)
1. 用一加万能工具包刷入TR：关机，开机时按住音量-和开机键，进入recovery模式【蓝色背景的第三方Rec-TR】
2. 找到终端输入*/mm，禁用太极模块
3. 重启

或者
1. 进入第三方Rec后，进入高级->文件管理器，直接删除data/adb文件，这样magisk模块都被清除[链接14楼](https://tieba.baidu.com/p/5792002894#121116896588l)

也可参考[少数派:刷 Magisk 模块手机「变砖」了？这三款工具能帮你救急.链接](https://sspai.com/post/57320)



一加7pro 安装Xposed方法[一加论坛地址](http://www.oneplusbbs.com/thread-4726573-1-23.html)：
安卓Pie9.0后传统的Xposed已经不能使用，所以通过Magisk安装EdXposed模块后安装Xposed框架
1. Magsk中安装Riru-Core模块的基础上，安装magisk-EdXposed-YAHFA-XXX模块，后重启手机
2. 安装EdXposedManager(原来需要XposedInstaller,作者后续统一升级为EdXposedManager)[github地址](https://github.com/ElderDrivers/EdXposed/blob/master/README_CN.md#%E6%A1%86%E6%9E%B6%E7%AE%A1%E7%90%86%E5%BA%94%E7%94%A8)
通过Magisk

术语解释:
1. TR, TWRP,Team Win Recovery Project, 其实就是Recovery， 主要用于刷机、备份、恢复等
2. Xposed，直接可以修改系统的一款框架。改动会影响在Google的SafetyNet保护下的app，比如银行类，PokemonGo，这些应用对root敏感
3. Magisk，Systemless的修改方式，在系统boot时将其img挂在到自己的分区下，构建一个虚拟文件系统，和system分区没半毛钱关系，即，不修改系统文件为前提，但是达到了修改系统文件的效果，通过这个方式可以绕过SafetyNet，系统OTA升级。但是延展性不如Xposed.[参考自](https://www.jianshu.com/p/59179c9124fa)
4. YAHAF，ART hook核心框架
5. Riru, 提供一种将代码注入zygote进程的方法
6. 太极，能不碰就不碰，毕竟吃过亏，见PS

PS：因为个人问题，直接 wipe 了整个data，特此记录。
