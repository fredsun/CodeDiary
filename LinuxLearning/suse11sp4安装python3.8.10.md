[toc]
# SUSE11SP4 编译Python3.8.10
## 前期确认
###  系统
基于纯净的suse11sp4, 下载地址: https://www.suse.com/download/sles/ ，下载文件 SLES-11-SP4-DVD-x86_64-GM-DVD1.iso
###  安装目录
以下一切文件均保存在目标目录 /home/data 

### IDE
pycharm2018.1.6
2016版本无法以为python38直接拉去setuptools
下载地址: 
https://www.jetbrains.com/pycharm/download/other.html
https://download.jetbrains.com/python/pycharm-professional-2018.1.6.tar.gz
社区版亦可

## 安装流程
0. 当前环境确认
1. 基础编译环境 gcc 更新
2. python38 编译环境补充
3. pycharm2018 安装，以及 pip 更新
4. 为安装watchfile 补充rust等环境

### 0. 纯净suse11sp4默认的环境确认

| 指令                           | 原装          | 目标          | 当前          | 结果          | 变动了 |
| ------------------------------ | ------------- | ------------- | ------------- | ------------- | ------ |
| gcc -v                         | 不存在        | 4.8.5         | 4.3.4         | 4.8.5         |
| ---python需要---               |
| pkg-config --modversion zlib   | 不存在        | 1.2.8         | 不存在        | 1.2.8         |
| openssl version                | 0.9.8j        | 1.0.2         | 不存在        | 1.0.2         |
| rpm -q libncurses6             | 5.6.90-55     | 5.6.90-55     | 5.6.90-55     | 5.6.90-55     |
| rpm -q gdbm                    | 1.8.3-374.25  | 1.8.3-374.25  | 1.8.3-374.25  | 1.8.3-374.25  |
| lzma -V                        | 不存在        | 5.2.3         | 不存在        | 5.2.3         |
| bzip2 --version                | 1.0.5         | 1.0.6         | 1.0.5         | 1.0.6         |
| sqlite3                        | 3.7.6.3       | 3.24.0        | 3.7.6.3       | 3.24.0        |
| readline #请用yast查询         | 5.2-147       |
| rpm -q libuuid1                | 2.19.1-6.72.1 | 2.20.1-1.8    | 2.19.1-6.72.1 | 2.20.1-1.8    |
| rpm -q libffi6                 | 不存在        | 3.0.11-3.18   | 不存在        | 3.0.11-3.18   |
| # pythin下tkinter需要:         |
| rpm -q tcl                     | 8.5.5-2.81    | 8.5.5-2.81    | 8.5.5-2.81    | 8.5.5-2.81    |
| rpm -q tk                      | 8.5.5-3.14.1  | 8.5.5-3.14.1  | 8.5.5-3.14.1  | 8.5.5-3.14.1  |
| rpm -q tcl-devel               | 不存在        | 8.5.5-2.7     | 不存在        | 8.5.5-2.7     |
| rpm -q tk-devel                | 不存在        | 8.5.5-3.1     | 不存在        | 8.5.5-3.1     |
| rpm -q xorg-x11-libX11         | 7.4-5.11.11.7 | 7.4-5.11.11.7 | 7.4-5.11.11.7 | 7.4-5.11.11.7 |
| rpm -q xorg-x11-libX11-devel   | 不存在        | 7.4-5.2       | 不存在        | 7.4-5.2       |
| rpm -q xorg-x11-proto-devel    | 不存在        | 7.4-58.1      | 不存在        | 7.4-58.1      |
| rpm -q xtrans                  | 不存在        | 1.2.7-12.1    | 不存在        | 1.2.7-12.1    |
| rpm -q xorg-x11-libXau         | 7.4-1.15      | 7.4-1.15      | 7.4-1.15      | 7.4-1.15      |
| rpm -q xorg-x11-libXau-devel   | 不存在        | 7.4-1.3       | 不存在        | 7.4-1.3       |
| rpm -q xorg-x11-libXdmcp       | 7.4-1.15      | 7.4-1.15      | 7.4-1.15      | 7.4-1.15      |
| rpm -q xorg-x11-libXdmcp-devel | 不存在        | 7.4-1.3       | 不存在        | 7.4-1.3       |
| rpm -q xorg-x11-libxcb         | 7.4-1.29.1    | 7.4-1.29.1    | 7.4-1.29.1    | 7.4-1.29.1    |
| rpm -q xorg-x11-libxcb-devel   | 不存在        | 7.4-1.3       | 不存在        | 7.4-1.3       |
| makeinfo --version             | 不存在        | 4.12          | 不存在        | 4.12          |
| ldd --version #glibc           | 2.11.3        | 2.18          | 2.11.3        | 2.18          |


#### 概念补充
纯净的suse11sp4是不具备gcc编译器的，可放心升级gcc版本，可使用`zypper se gcc`和`zypper se gcc-c++`确认版本

## 开始安装
### 1.gcc：4.8.5
需要用到zyper里的gcc4.3.4来安装，即`zypper in gcc`和`zypper in gcc-c++`先执行，才能使用如下的confugure/make等命令，zypper指令默认挂载ios1，无法执行考虑自行挂载
   ```
   zypper in gcc
   zypper in gcc-c++
   tar -xvf gcc-4.8.5.tar.gz
   cd gcc-4.8.5
   ./contrib/download_prerequisites #执行这个文件，自行下载编译所需环境地址分别是 
   #ftp://gcc.gnu.org/pub/gcc/infrastructure/mpfr-2.4.2.tar.bz2 
   #ftp://gcc.gnu.org/pub/gcc/infrastructure/gmp-4.3.2.tar.bz2   
   #ftp://gcc.gnu.org/pub/gcc/infrastructure/mpc-0.8.1.tar.gz
   #自行下载后解压移动到 gcc-4.8.5
	tar -xvf mpfr-2.4.2.tar.bz2 -C  /home/data/gcc-4.8.5
	tar -xvf gmp-4.3.2.tar.bz2 -C  /home/data/gcc-4.8.5
	tar -xvf mpc-0.8.1.tar.gz -C  /home/data/gcc-4.8.5
   cd /home/data/gcc-4.8.5
   # 注意，以下三句指令，是给当前目录创建符号 mprf,gmp,mpc，并链接它们的位置，一定要在gcc-4.8.5目录下执行
 	ln -sf mpfr-2.4.2 mpfr
   ln -sf gmp-4.3.2 gmp
   ln -sf mpc-0.8.1 mpc
   cd ..
   mkdir gcc-4.8.5-build  #该文件夹与gcc-4.8.5平级
   cd gcc-4.8.5-build
   ../gcc-4.8.5/configure --prefix=/usr/local/gcc --enable-languages=c,c++ --build=x86_64-linux --disable-multilib
	# 上述 /usr/local/gcc 为本次新建
   LD_LIBRARY_PATH="$LD_LIBRARY_PATH:/usr/local/lib"
   export LD_LIBRARY_PATH 
   make -j8 && make install
   export PATH=/usr/local/gcc/bin:$PATH    # 在当前对话中环境下，将添加进~/.bashrc中, 临时 
   # 路径映射到全局, 永久, 建议此时退出su设置
   vi ~/.bashrc
   export PATH=/usr/local/gcc/bin:$PATH
   source ~/.bashrc
   gcc -v
   4.8.5 # 确认完成
   ```

### 2.python包首次编译确认缺失环境
1. 解压 python
```
cd /home/data
tar -xvf Python-3.8.10.tgz 
```
2. 编译
```
cd Python-3.8.10
./configure --prefix=/usr/local/python38
make
```
3. 确认缺失包
```
Python build finished successfully!
The necessary bits to build these optional modules were not found:
_bz2                  _curses               _curses_panel      
_dbm                  _gdbm                 _hashlib           
_lzma                 _sqlite3              _ssl               
_tkinter              _uuid                 readline           
zlib                                                           
To find the necessary bits, look in setup.py in detect_modules() for the module's name.
```

### 3.安装python缺失包
   1. zlib, 需>1.1.4 
   ```
    wget https://zlib.net/fossils/zlib-1.2.8.tar.gz
    tar -xvf zlib-1.2.8.tar.gz
    cd zlib-1.2.8
    ./configure
    make
    make install
    pkg-config --modversion zlib
	1.2.8 #确认安装完成
   ```
   2. openssl, 需>1.0.2 安装1.0.2， 参考 https://blog.csdn.net/wujingenli/article/details/84549259
   ```
   wget https://www.openssl.org/source/old/1.0.2/openssl-1.0.2.tar.gz
   tar -xvf openssl-1.0.2.tar.gz
   cd openssl-1.0.2/
   ./config  --prefix=/usr/local --openssldir=/usr/local/ssl
   make && make install
   ./config shared --prefix=/usr/local --openssldir=/usr/local/ssl
   make clean
   make && make install
   openssl version 
	OpenSSL 1.0.2 22 Jan 2015 #确认安装完成
   ```

   3. 基本开发包，解决_curses , _curses_panel ,_dbm  ,  _gdbm 
   ```
   zypper install -t pattern Basis-Devel
   ```
   4. lzma
   ```
    wget http://nchc.dl.sourceforge.net/project/lzmautils/xz-5.2.3.tar.gz
    tar -zxvf xz-5.2.3.tar.gz
    cd xz-5.2.3/
    ./configure
    make 
    make install 
    lzma -V  
      5.2.3 #确认安装完成
    ```
    5. bz2
    ```
    https://src.fedoraproject.org/lookaside/pkgs/bzip2/bzip2-1.0.6.tar.gz/00b516f4704d4a7cb50a1d97e6e8e15b/
    tar -zxvf bzip2-1.0.6.tar.gz
    cd bzip2-1.0.6/
    make -f Makefile-libbz2_so  CFLAGS="-fPIC"
    make install PREFIX=/usr/local   #/usr/local 是默认位置
	bzip2 --version 
	# bzip2, a block-sorting file compressor.  Version 1.0.6, 6-Sept-2010.
	# 确认安装完成

    ```
    6. sqlite3 本身自带3.7.6.3,但是缺少devel包
    ```
    wget https://www.sqlite.org/2018/sqlite-autoconf-3240000.tar.gz
    速度太慢，直接外部下载后复制进去
    tar -xvzf sqlite-autoconf-3240000.tar.gz
    cd sqlite-autoconf-3240000
    ./configure
    make
    make install
    测试 which sqlite3 || 直接输入sqlite3,确认进入命令行SQLiteVersion3.24.0，退出 ctrl + D
    ```

    7. readline 6.2.4
    ```
   wget https://github.com/ludwigschwardt/python-gnureadline/archive/refs/tags/v6.2.4.1.tar.gz
   tar -zxvf python-gnureadline-6.2.4.1.tar.gz
   cd python-gnureadline-6.2.4.1/rl
   tar xzf readline-6.2.tar.gz 
   cd readline-6.2/
   ./configure&&make&&make install
   # readline可能会报错mv /usr/local/lib/libreadline.a /usr/local/lib/libreadline.old
   mv: 无法获取"/usr/local/lib/libreadline.a" 的文件状态(stat): 没有那个文件或目录
   make: [install-static] 错误 1 (忽略)， 这是在备份libreadline.a，原因是没刷新共享库导致文件存在也无法被共享库找到。不用管
    ```


   8. uuid
   ```
   rpm -e libuuid1 --nodeps
   rpm -ivh libuuid1-2.20.1-1.8.test.1.x86_64.rpm
   rpm -ivh libuuid-devel-2.20.1-1.8.test.1.x86_64.rpm
   ```

   9. libffi
   ```
   rpm -ivh libffi6-3.0.11-3.18.x86_64.rpm 
   rpm -ivh libffi-devel-3.0.11-3.18.x86_64.rpm 
   ```



   10.  tkinter 
   以下记录了完整流程，安装仅需复制`rpm -ivh xxxx`部分
   ```
   rpm -ivg tcl-devel-8.5.5-2.7.x86_64.rpm
      rpm -ivh python-xcb-proto-devel-7.4-58.1.x86_64.rpm
      rpm -ivh xorg-x11-proto-devel-7.4-58.1.x86_64.rpm
      rpm -ivh xorg-x11-libXau-devel-7.4-1.3.x86_64.rpm
      rpm -ivh xorg-x11-libxcb-devel-7.4-1.3.x86_64.rpm 
      rpm -ivh xorg-x11-libXdmcp-devel-7.4-1.3.x86_64.rpm
      rpm -ivh xtrans-1.2.7-12.1.noarch.rpm
   rpm -ivh xorg-x11-libX11-devel-7.4-5.2.x86_64.rpm 
   rpm  -ivh tk-devel-8.5.5-3.1.x86_64.rpm 
   ```

###  4.再次安装python
建议重新解压后重新安装
```
cd /home/data
rm -rf Python-3.8.10
tar -xvf Python-3.8.10.tgz 
cd Python-3.8.10
./configure --prefix=/usr/local/python38 --enable-shared
make 
make install
ln -s /usr/local/python38/bin/python3.8 /usr/bin/python38
ln -s /usr/local/python38/bin/pip3.8 /usr/bin/pip3
cp /usr/local/python38/lib/libpython3.8.so.1.0 /usr/lib64/
cp /usr/local/python38/lib/libpython3.8.so.1.0 /usr/lib/
将libptython3.8.so 和.a相关都复制到lib和lib64下

```

### 5.pycharm安装
记得开启su
```
tar -xvf pycharm-professional-2018.1.6.tar.gz 
cd pycharm-2018.1.6/bin/
su
# 为了不影响后续输入命令，请新开启shell单独运行启动pycharm的命令
sh pycharm.sh 
```
### 6.更新pip
pycharm设置根据py3.8.10目录里的python38建立虚拟环境
1. 下载
```
# 有网环境直接安装
pip install pip==20.2.4
pip install setuptools==68.2.2

# 无网环境下先用有网的机器下载转移后，再更新
pip download pip==20.2.4
pip download setuptools==68.2.2
在无网的环境下安装
python -m pip install --upgrade /home/data/pip-20.2.4-py2.py3-none-any.whl 
python -m pip install --upgrade /home/data/setuptools-68.2.2-py3-none-any.whl 
或者生成 requirements
pip3 freeze --all > requirements.txt
pip3 download -d ./EnvPypi/ -r requirements.txt 
后安装
pip install --no-index --find-links=/home/data/EnvXXXX -r /home/data/EnvXXXX/requirements.txt 
```

#### watchfile 安装过程中，报错 maturin 的编译环境缺少 rust，本可以升级 pycharm2023 解决。
但是suse11sp4无法安装pycharm2023, 需要解决realpath环境，于是本地编译maturin, 需要安装rust, 又需要高版本 glibc, glibc 需要安装高版本 texInfo。
所以安装流程是反过来, 更新texInfo, 安装高版本 glibc, 安装 rust, 安装 maturin, 安装 watchfile

### 7.更新texInfo 4.7
```
tar -xvf texinfo-4.7.tar.gz
cd texinfo-4.7
./configure --prefix=/usr/local/texinfo
make
make install
export PATH=/usr/local/texinfo/bin:$PATH 
makeinfo --version
# makeinfo (GNU texinfo) 4.7
安装完成
```
### 8.安装glibc, 注意命令结尾别添加额外空格
```
tar -xvf glibc-2.18.tar.gz
cd glibc-2.18
mkdir build
cd build
../configure --prefix=/usr --disable-profile --enable-add-ons --with-headers=/usr/include --with-binutils=/usr/bin
make 
make install
ldd --version
# ldd (GNU libc) 2.18
# 安装完成
如果出现报错 LD_LIBRARY_PATH shouldn't contain the current directory when building glibc. ，说明当前路径下LD_LIBRARY_PATH被占用了，清理了即可
echo $LD_LIBRARY_PATH
:/usr/local/lib  #确认被占用
export LD_LIBRARY_PATH=   
echo $LD_LIBRARY_PATH 
# 无返回值时确认没被占用
```
### 9.安装rust
```
https://static.rust-lang.org/dist/rust-1.73.0-x86_64-unknown-linux-gnu.tar.gz
tar -xvf rust-1.73.0-x86_64-unknown-linux-gnu.tar.gz 
cd rust-1.73.0-x86_64-unknown-linux-gnu
sh install.sh
```
### 10.安装watchfile等pypi包, 包括maturin
```
有网环境下载watchfile包，存到/home/local/watchFilesEnv 转移给无网机器
pip download watchfiles==0.18.0 
#下载如下5个包
#watchfiles-0.18.1-cp37-abi3-manylinux_2_17_x86_64.manylinux2014_x86_64.whl 
#anyio-4.0.0-py3-none-any.whl 
#exceptiongroup-1.1.3-py3-none-any.whl
#sniffio-1.3.0-py3-none-any.whl 
#dna-3.4-py3-none-any.whl
导出安装环境文件，只保留如上5个包
pip freeze > requirements.txt
无网环境复制/home/local/EnvWatchFiles整体，包括5个包和requirements，在新环境安装
pip3 install --no-index --find-links=/home/data/EnvWatchfiles -r /home/data/EnvWatchfiles/requirements.txt 
```

## 完成



### PS
#### gcc 未挂载
表现为媒体源xxx不包含所需的媒体，请插入媒体xxxxx。例如, zypper 可能挂载了dvd2，但是gcc在dvd1源里。
1. 挂载（解压）dvd1.ios，可能有权限问题, 给777
mount -o loop /home/SLES-11-SP4-DVD-x86_64-GM-DVD1 /home/repoTemp
2. 挂载后的文件夹repoTemp为只读，复制一个
cp -rf /home/repotemp/* /home/repo
3. 解压后的源设置为zypper仓库, 取名localStore
zypper ar /home/repo localStore




