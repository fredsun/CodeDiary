## 安装方式
Homebrew or Git拉库安装
* Homebrew 安装参考自知乎的这篇["血泪总结"](https://zhuanlan.zhihu.com/p/90099862Â),  因为从brew2.0版本开始不再针对ffmpeg的options进行一键关联，如果需要将options和ffmpehg关联起来的话需要第三方的options库。所以要使用x264,vp9等视频编码器，需要用到三方options库。不过目前三方库的更新很及时。
  - 安装流程为:brew install -> 选择第三方库安装options和ffmpeg关联

* git在本地安装则之需要本地编译即可[简书:Mac系统上安装FFmpeg](https://www.jianshu.com/p/ab469a2ffd28)
  - 安装流程为:git clone -> 编译  ->  安装 -> 配置环境变量

## 安装流程
(完全引用原文做备份)
### 1. 下载FFmpeg
cd到安装目录, 如ffm
```
git clone https://git.ffmpeg.org/ffmpeg.git
```

### 2. 编译FFmpeg
进入ffm/ffmpeg目录
配置configure
```
./configure --prefix=/usr/local/ffmpeg  --enable-gpl  --enable-nonfree  --enable-libfdk-aac  --enable-libx264  --enable-libx265 --enable-filter=delogo --enable-debug --disable-optimizations --enable-libspeex --enable-videotoolbox --enable-shared --enable-pthreads --enable-version3 --enable-hardcoded-tables --cc=clang --host-cflags= --host-ldflags=
```
如果报错nasm/yasm not found or too old. Use --disable-x86asm for a crippled build的话，先执行下面命令安装yasm然后再执行配置configure的命令。
```
brew install yasm
```
如果报错ERROR: libfdk_aac not found的话，先执行下面命令安装fdk-aac然后再执行配置configure的命令。
```
brew install fdk-aac
```

成功后显示
License: nonfree and unredistributable

### 3. 安装FFmpeg
```
make && make install

// 如果安装出现下图的错误的话就用这条命令来安装
9 warnings generated.
LD	ffprobe_g
STRIP	ffprobe
mkdir: /usr/local/ffmpeg/lib: Permission denied
make: *** [install-libavdevice-static] Error 1

//如下命令重装, 需要两句sudo
make clean
sudo make && sudo make install
```
安装成功后ffmpeg所在的目录是/usr/local/ffmpeg
安装完提示如下的话，说明需要家
```

```

### 4. 配置环境变量
```
vi ~/.bash_profile
//配置ffmpeg的bin目录
export PATH=$PATH:/usr/local/ffmpeg/bin
//输入:wq保存退出
source ~/.bash_profile
```
