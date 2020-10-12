### 前置
* Mac 自带 BootCamp 提供给 Win 系统驱动
* BootCamp 不允许外置存储设备
![](https://raw.githubusercontent.com/fredsun/RES/66407cc8d1c00d624cdfac81c12fc3c11568972e/bootcamp.png)

* 所以将 BootCamp 分别安装在 Mac 和 Win 上, Win 安装成 WinToGo
* 此教程须固态移动硬盘/U盘， **Windows 真机**，模拟器尝试过 VMware，失败，建议尝试其他虚拟机

### 准备
1. 下载BootCamp, [地址](https://www.applex.net/pages/bootcamp/), 我下载的是6.1.6851, 如果无法安装，会给提示，建议版本不明的多下载几个。
![](https://raw.githubusercontent.com/fredsun/RES/66407cc8d1c00d624cdfac81c12fc3c11568972e/bootcamp_win_list.png)
2. 下载 WinToGO [地址](https://bbs.luobotou.org/thread-761-1-1.html)

3. 下载 Win10, 官网即可
4. 备份固态硬盘里的文件，我自己尝试过两次，第一次数据全抹掉，第二次数据没变化，应该和硬盘的文件系统有关

### 安装
1. 打开 Windows 电脑，插入移动硬盘后，打开 WinToGo， 不如图配置，**勾选 UEFI+GPT** 默认会将你全部的移动硬盘都作为 Windows 可使用的大小
![](https://raw.githubusercontent.com/fredsun/RES/66407cc8d1c00d624cdfac81c12fc3c11568972e/luobotou_wintogo.jpg)
2. 完成后重启 Mac, 开机时按住 Option 不松 出现引导画面




### tips
* 建议固态硬盘，本身速度快，且 USB3.0接口，
* 如果硬盘也需要 Mac 使用，安装 Tuxera NTFS 即可读写移动硬盘
* 使用过程中如果系统提示安装BootCamp驱动更新请尽早安装，体感安装后显卡利用率提高了，风扇转的不那么多了
* win 系统过去使用 BIOS+MBR 的系统安装方式
* win8 预装系统开始统一采用 UEFI+GPT
* GPT 对比 MBR
  - 都是固件接口标准
  - GPT：全局唯一标识分区表(GUID Partition Table）
  - MBR：512字节的Master Boot Record（主引导记录）中的分区表，由于大小限制，其中只能存有最多四个分区的描述（亦即4个主分区）
  - GPT能使用大于2.2T的硬盘
  - GPT可支持无限的主分区，没有逻辑分区的概念。MBR最多4个主分区，但是微软只认128个
  - GPT分区磁盘有备份分区表提供数据结构完整性，一旦被病毒感染或者误操作，可以通过备份的分区表补救
* UEFI & BIOS
  - Unified Extensible Firmware Interface，架设在系统固件之上的软件接口
* UEFI+GPT 开机启动快，跳过外设检测，
* UEFI+GPT 支持 Secure Boot, 抵御Bootkit攻击
* UEFI BIOS 支持鼠标操作
* UEFI+GPT 强制要求把引导文件放在ESP分区，且操作系统必须在另外的主分区，BIOS的系统引导文件和系统文件可以放在同一个分区的根目录
* UEFI+GPT的系统引导文件所在的ESP分区直接不可见，BIOS + MBR 的系统引导文件分区可设置可见，也可隐藏
* 传统(Legacy)启动系统流程:
![](https://raw.githubusercontent.com/fredsun/RES/66407cc8d1c00d624cdfac81c12fc3c11568972e/bios.png)
* UEFI启动系统流程:
![](https://raw.githubusercontent.com/fredsun/RES/66407cc8d1c00d624cdfac81c12fc3c11568972e/uefi.png)
* BIOS+MBR: 可用，可启动过系统，最常见，但是不支持容量大于2T的硬盘
* BIOS+GPT：可用，但是无法启动系统，一般作为音乐发烧友的BIOS+MBR装系统，BIOS+GPT存储文件
* UEFI+MBR：将UEFI设置为legacy模式，效果=BIOS+MBR，无意义
* UEFI+GPT:可用，可启动系统，未来趋势

### 参考
[萝卜头] https://bbs.luobotou.org/thread-761-1-1.html
[MacBook Pro 外置硬盘使用windows to go 实现BootCamp] https://post.smzdm.com/p/apzkqx29/
[知乎: UEFI+GPT与BIOS+MBR各自有什么优缺点？] https://www.zhihu.com/question/28471913
[知乎: BIOS, UEFI, MBR, Legacy, GPT等概念整理]https://zhuanlan.zhihu.com/p/36976698
[少数派 说走就走的「Windows」—— Windows To Go 制作详解]https://sspai.com/post/44699
