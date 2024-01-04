编译python3.8.10
   1. 准备环境， gcc gcc-c++ zlib-devel libopenssl-devel，从 setup.py 中可确认各软件具体版本
   2. 确认编译工具gcc和gcc-c++, 
   ```gcc --version`` , 4.3.4, 如果没安装，那么 
   ```zypper install gcc   zypper install gcc-c++```
    如果没有gcc强行安装，那么报错`Checking for gcc...
Compiler error reporting is too harsh for ./configure (perhaps remove -Werror).
** ./configure aborting.`
1. 第一次尝试编译python3.8
```
tar -zxvf Python-3.8.10.tgz
./configure --prefix=/usr/local/python38 --enable-optimizations  
make
```
注意 不要添加 `--enable-optimizations`，否则报错
```
cc1: 错误：无法识别的命令行选项“-fprofile-correction”
make[1]: *** [Programs/python.o] 错误 1
make[1]: Leaving directory `/home/local/Python-3.8.10'
make: *** [profile-opt] 错误 2
```
返回

```
Python build finished successfully!
The necessary bits to build these optional modules were not found:
_bz2                  _curses               _curses_panel      
_dbm                  _gdbm                 _hashlib           
_lzma                 _sqlite3              _ssl               
_tkinter              _uuid                 readline           
zlib                                                           
To find the necessary bits, look in setup.py in detect_modules() for the module's name.


The following modules found by detect_modules() in setup.py, have been
built by the Makefile instead, as configured by the Setup files:
_abc                  atexit                pwd                
time                                                           


Failed to build these modules:
_ctypes 
```
补充zlib，openssl，readline                       
   1. zlib, 需>1.1.4 
   ```
    wget https://zlib.net/fossils/zlib-1.2.8.tar.gz
    tar -xvf zlib-1.2.8.tar.gz
    cd zlib-1.2.8
    ./configure
    make
    make install
   ```
   2. openssl, 需>1.0.2 安装1.0.2， 参考 https://blog.csdn.net/wujingenli/article/details/84549259
   ```
   wget https://www.openssl.org/source/old/1.0.2/openssl-1.0.2.tar.gz
   tar -xvf openssl-1.0.2.tar.gz
   ./config  --prefix=/usr/local --openssldir=/usr/local/ssl
   make && make install
   ./config shared --prefix=/usr/local --openssldir=/usr/local/ssl
   make clean
   make && make install
   openssl version //查看
   ```
   3. readline, 尝试6.2.4.1
   ```
   wget https://github.com/ludwigschwardt/python-gnureadline/archive/refs/tags/v6.2.4.1.tar.gz
   tar -zxvf python-gnureadline-6.2.4.1.tar.gz
   tar xzf readline-6.2.4.1.tar.gz 
   cd readline-6.2.4.1/rl/
   tar xzf readline-6.2.tar.gz 
   cd readline-6.2/
   ./configure&&make&&make install
   ```

第二次硬装
```
Python build finished successfully!
The necessary bits to build these optional modules were not found:
_bz2                  _curses               _curses_panel      
_dbm                  _gdbm                 _lzma              
_sqlite3              _tkinter              _uuid              
To find the necessary bits, look in setup.py in detect_modules() for the module's name.


The following modules found by detect_modules() in setup.py, have been
built by the Makefile instead, as configured by the Setup files:
_abc                  atexit                pwd                
time                                                           


Failed to build these modules:
_ctypes                                                        


Following modules built successfully but were removed because they could not be imported:
readline                                                       

```


第三次硬装, 验证基本开发包 Basis-Devel
于是补充基本开发包
```
zypper install -t pattern Basis-Devel
```
基本开发包解决了 _curses , _curses_panel ,_dbm  ,  _gdbm  , 似乎也顺手解决了readline的问题

```
Python build finished successfully!
The necessary bits to build these optional modules were not found:
_bz2                  _lzma                 _sqlite3           
_tkinter              _uuid                                    
To find the necessary bits, look in setup.py in detect_modules() for the module's name.


The following modules found by detect_modules() in setup.py, have been
built by the Makefile instead, as configured by the Setup files:
_abc                  atexit                pwd                
time                                                           


Failed to build these modules:
_ctypes   
```



第四次硬装, 验证_lzma解决
下载lzma, 
```
wget http://nchc.dl.sourceforge.net/project/lzmautils/xz-5.2.3.tar.gz
tar -zxvf xz-5.2.3.tar.gz
./configure
make 
make install 
lzma -V  => 5.2.3
lzma 解决
```

第五次硬装, bzip2解决_bzip2
bzip2 下载
```
https://src.fedoraproject.org/lookaside/pkgs/bzip2/bzip2-1.0.6.tar.gz/00b516f4704d4a7cb50a1d97e6e8e15b/
tar bz2 -zxvf bzip2-1.0.6.tar.gz
cd bz2-1.0.6
make -f Makefile-libbz2_so  CFLAGS="-fPIC"
make install PREFIX=/usr/local   #/usr/local 是默认位置
# 不确定是否用到了bz2-devel不过已经安装了
wget ftp://ftp.pbone.net/mirror/ftp5.gwdg.de/pub/opensuse/repositories/home%3A/zhy20120210%3A/SLES-11-SP1-x86-64/SLE_11_SP1/x86_64/libbz2-devel-1.0.5-34.10.x86_64.rpm
```


第六次硬装，解决_sqlite3
```
wget https://www.sqlite.org/2018/sqlite-autoconf-3240000.tar.gz
速度太慢，直接外部下载后复制进去
tar -xvzf sqlite-autoconf-3240000.tar.gz
cd sqlite-autoconf-3240000
./configure
make
make install
测试 which sqlite3/直接输入sqlite3,确认进入命令行，退出 ctrl + D
```
返回结果
```
Python build finished successfully!
The necessary bits to build these optional modules were not found:
_tkinter              _uuid                                    
To find the necessary bits, look in setup.py in detect_modules() for the module's name.


The following modules found by detect_modules() in setup.py, have been
built by the Makefile instead, as configured by the Setup files:
_abc                  atexit                pwd                
time                                                           


Failed to build these modules:
_bz2                  _ctypes   
```

第七次硬装，解决_uuid
实现：因是编译时需要，单纯的 util-linux 下的uuid不够，需要uuid.h头文件，通过安装 libuuid-devel 即可生成 /usr/include/uuid以及里面的uuid.h 供 python 使用
已经安装uuid版本: 
libuuid1: 2.19.1-6.72.1
libuuid1-32bit: 2.19.1-6.72.1
uuid-runtime-2.19.1-6.72.1
以上uuid包随 util-linux-2.19.1-6.72.1.x86_64 一起安装，版本号统一
于是尝试单独补充 libuuid-devel
最佳处理：安装 libuuid-devel-2.19.1-6.72.1.x86_64 补充/usr/include/uuid.h, github有人提及改包，但是找不到该版本的包`https://github.com/linux-nvme/nvme-cli/issues/560`
rpm包网站`https://software.opensuse.org/package/libuuid1`
1. 安装 libuuid-devel
   1. 2.19.1-6.72.1 最佳版本，找不到
   2. 2.19.1-1.4.fc5 ,非适配suse的包 小版本号不匹配需要libuuid1=2.19.1-1.4.fc5, 且rpm<4.6
   3. 2.19-10.1 版本过低
   4. 2.23.2-23 版本过高
   5. 2.20.1-1.8 版本过高，需要libuuid1版本=2.20.1
 
2. 升级 libuuid1 版本
   查看`rpm -qi libuuid1`，版本为2.19.1-6.72.1
   1. 2.20.1-1.8.test.1
      1. 安装后提示libuuid.so.1.3.0和libuuid.so.1()(64bit)冲突
         ```
         rpm -ivh libuuid1-2.20.1-1.8.test.1.x86_64.rpm 
               Preparing...                ########################################### [100%]
         file /lib64/libuuid.so.1.3.0 from install of libuuid1-2.20.1-1.8.test.1.x86_64 conflicts with file from package libuuid1-2.19.1-6.72.1.x86_64
         ```
      2. 尝试卸载`rpm -e libuuid1`，提示libuuid.so.1()(64bit)被86个依赖相关
      3. 但是libuuid1-2.20.1-1.8.test.1.x86_64.rpm 下载的地址中指出新版本依然会提供libuuid.so.1()(64bit)地址: `https://rpm.pbone.net/info_idpl_23882870_distro_opensuse_com_libuuid1-2.20.1-1.8.test.1.x86_64.rpm.html`, 于是尝试强行卸载
      ```
      rpm -e libuuid1 --nodeps
      rpm -ivh libuuid1-2.20.1-1.8.test.1.x86_64.rpm
      rpm -ivh libuuid-devel-2.20.1-1.8.test.1.x86_64.rpm
      ```
      此时查看到/usr/include下uuid 文件夹生成，里面的uuid.h文件生成
      硬装后结果
      ```
      Python build finished successfully!
      The necessary bits to build these optional modules were not found:
      _tkinter                                                       
      To find the necessary bits, look in setup.py in detect_modules() for the module's name.


      The following modules found by detect_modules() in setup.py, have been
      built by the Makefile instead, as configured by the Setup files:
      _abc                  atexit                pwd                
      time                                                           


      Failed to build these modules:
      _bz2                  _ctypes      

      ```

   2. 安装新版本 uuid-runtime 2.20, `rpm -ivh util-linux-2.20.1-1.8.test.1.x86_64.rpm`
   error: Failed dependencies:
   	libaudit.so.1()(64bit) is needed by util-linux-2.20.1-1.8.test.1.x86_64
   	libblkid.so.1(BLKID_2.20)(64bit) is needed by util-linux-2.20.1-1.8.test.1.x86_64
   	libc.so.6(GLIBC_2.14)(64bit) is needed by util-linux-2.20.1-1.8.test.1.x86_64  -> 如果更新2.14，需要动37个包
   	libmount.so.1(MOUNT_2.20)(64bit) is needed by util-linux-2.20.1-1.8.test.1.x86_64
3. uuidd-2.19-3.6.1.x86_64 ，版本太低
4. 尝试手动编译libuuid-devel-2.19.1-6.72.1, 通过拆包前后两版本 2.19-10.1 和 2.20.1-1.8
   1. 环境准备
      1. rpmrebuild-2.12 https://sourceforge.net/projects/rpmrebuild/files/rpmrebuild/2.12-1/
      2. rpmbuild

可能需要补充 libuuid1-2.19.1-6.72.1.x86_64

3. 尝试安装 util-linux-2.19.1-rc1.tar.gz
`tar -xvf util-linux-2.19.1-rc1.tar.gz` 解压后 并无./configure，目录结构是makefile.am, 
须使用autoreconf -i -f 命令生成configure文件
执行`autoconf -i`，报错缺少 autopoint


第八次硬装，为了_tkinter
```
wget ftp://ftp.pbone.net/mirror/ftp5.gwdg.de/pub/opensuse/repositories/home%3A/zhy20120210%3A/SLES-11-SP1-x86-64/SLE_11/x86_64/tk-devel-8.5.5-3.1.x86_64.rpm
rpm  -ivh tk-devel-8.5.5-3.1.x86_64.rpm 
warning: tk-devel-8.5.5-3.1.x86_64.rpm: Header V3 DSA signature: NOKEY, key ID 034e6912
error: Failed dependencies:
	tcl-devel is needed by tk-devel-8.5.5-3.1.x86_64
	xorg-x11-libX11-devel is needed by tk-devel-8.5.5-3.1.x86_64
```

YaST 查看确认已有 tk-8.5.5-3.14.1 , tcl-8.5.5-2.81
需要安装 tk-devel, tcl-devel, 顺序注意是先 tcl
```
wget ftp://ftp.pbone.net/mirror/ftp5.gwdg.de/pub/opensuse/repositories/home%3A/zhy20120210%3A/SLES-11-SP1-x86-64/SLE_11_SP1/x86_64/tcl-devel-8.5.5-2.7.x86_64.rpm
rpm -ivg tcl-devel-8.5.5-2.7.x86_64.rpm
```
xorg-x11-libX11-devel 需要下面5个包
```
wget ftp://ftp.pbone.net/mirror/ftp5.gwdg.de/pub/opensuse/repositories/home%3A/zhy20120210%3A/SLES-11-SP1-x86-64/SLE_11/x86_64/xorg-x11-libX11-devel-7.4-5.2.x86_64.rpm
rpm -igh xorg-x11-libX11-devel-7.4-5.2.x86_64.rpm 
warning: xorg-x11-libX11-devel-7.4-5.2.x86_64.rpm: Header V3 DSA signature: NOKEY, key ID 034e6912
error: Failed dependencies:
	xorg-x11-proto-devel is needed by xorg-x11-libX11-devel-7.4-5.2.x86_64
	xorg-x11-xtrans-devel is needed by xorg-x11-libX11-devel-7.4-5.2.x86_64
	xorg-x11-libXau-devel is needed by xorg-x11-libX11-devel-7.4-5.2.x86_64
	xorg-x11-libXdmcp-devel is needed by xorg-x11-libX11-devel-7.4-5.2.x86_64
	xorg-x11-libxcb-devel is needed by xorg-x11-libX
```

0. proto
xorg-x11-proto-devel 又需要 python-xcb-proto-devel = 7.4
```
wgetftp://ftp.pbone.net/mirror/ftp5.gwdg.de/pub/opensuse/repositories/home%3A/linux4humans%3A/sle11_software%3A/M17N/SLE_11_SP4/x86_64/python-xcb-proto-devel-7.4-58.1.x86_64.rpm
wget ftp://ftp.pbone.net/mirror/ftp5.gwdg.de/pub/opensuse/repositories/home%3A/linux4humans%3A/sle11_software%3A/M17N/SLE_11_SP4/x86_64/xorg-x11-proto-devel-7.4-58.1.x86_64.rpm
```
1. xau
```
wget ftp://ftp.pbone.net/mirror/ftp5.gwdg.de/pub/opensuse/repositories/home%3A/zhy20120210%3A/SLES-11-SP1-x86-64/SLE_11_SP1/x86_64/xorg-x11-libXau-devel-7.4-1.3.x86_64.rpm
```
2. xcb
```
wget ftp://ftp.pbone.net/mirror/ftp5.gwdg.de/pub/opensuse/repositories/home%3A/linux4humans%3A/sle11_software%3A/M17N/SLE_11_SP4/x86_64/python-xcb-proto-devel-7.4-58.1.x86_64.rpm
```
3. xdmcp
```   
wget ftp://ftp.pbone.net/mirror/ftp5.gwdg.de/pub/opensuse/repositories/home%3A/zhy20120210%3A/SLES-11-SP1-x86-64/SLE_11_SP1/x86_64/xorg-x11-libXdmcp-devel-7.4-1.3.x86_64.rpm
```

4. xtrans
suse 没有 xorg-x11-xtrans-devel 包，直接安装 xtrans 即可
```
wget ftp://ftp.pbone.net/mirror/ftp5.gwdg.de/pub/opensuse/repositories/home%3A/Podeiko%3A/X11%3A/XOrg/openSUSE_12.1/noarch/xtrans-1.2.7-12.1.noarch.rpm
```


第八次硬装 解决_ctype
安装libffi和libffi-devel
```
wget ftp://ftp.pbone.net/mirror/ftp5.gwdg.de/pub/opensuse/repositories/home%3A/frispete%3A/python3/openSUSE_11.1/x86_64/libffi6-3.0.11-3.18.x86_64.rpm
wget ftp://ftp.pbone.net/mirror/ftp5.gwdg.de/pub/opensuse/repositories/home%3A/frispete%3A/python3/openSUSE_11.1/x86_64/libffi-devel-3.0.11-3.18.x86_64.rpm
```

安装完提示
make 
make install
修改软链接
```
ln -s /usr/local/python38/bin/python3.8 /usr/bin/python38
ln -s /usr/local/python38/bin/pip3.8 /usr/bin/pip3
```
第一次尝试进入，报错找不到readline
```
python38
Python 3.8.10 (default, Oct  8 2023, 23:49:28) 
[GCC 4.3.4 [gcc-4_3-branch revision 152973]] on linux
Type "help", "copyright", "credits" or "license" for more information.
Traceback (most recent call last):
  File "/etc/pythonstart", line 7, in <module>
    import readline
ModuleNotFoundError: No module named 'readline'
>>> ^C
KeyboardInterrupt
>>> 
```
当前 readline 版本5-5.2-147.3
1. libreadline5-5.2-147.3 显示已安装，并且覆盖安装
2. readline-devel-5.2-147.3.x86_64 显示已安装，并且覆盖安装
3. ncurses-devel 5.6-90.55
4. libncurses5 5.6-90.55

<!-- 1. 尝试重装openssl，原c语言编译方式都替换为rpm包安装
```
wget ftp://ftp.pbone.net/mirror/ftp5.gwdg.de/pub/opensuse/repositories/home%3A/aljex/openSUSE_11.4/x86_64/openssl-1.0.2n-2.58.1.x86_64.rpm
wget ftp://ftp.pbone.net/mirror/ftp5.gwdg.de/pub/opensuse/repositories/home%3A/aljex/openSUSE_11.4/x86_64/libopenssl-devel-1.0.2n-2.58.1.x86_64.rpm
```
openssl需要libssl.so和libcrypto.so -->

尝试在 python38 环境安装 gnureadline6.3.8
1. 下载 https://pypi.org/project/gnureadline/6.3.8/
   ```
   tar -xvf gnureadline-6.3.8.tar.gz 
   cd gnureadline-6.3.8.tar.gz 
   python38 setup.py install

   ```
   报错 
   ```
   ModuleNotFoundError: No module named '_ctypes'
   ```
   已安装 libffi6-3.0.11-3.18 和 libffi-devel-3.0.11-3.18，suse官方repo库中也是 libffi6-3.0.11-2.1 和 libffi-devel-3.0.11-2.1

   尝试将之前自己编译的库都使用rpm安装
   ==============
   tar zxf Python-3.8.5.tgz
   cd Python-3.8.5
   export LD_LIBRARY_PATH=/usr/local/lib && \
   export LD_RUN_PATH=/usr/local/lib && \
   ./configure --prefix=/usr/local/python38 --with-openssl=/usr/local/openssl111 --enable-shared --with-system-ffi=/usr/local/lib/
   make
   make install

   ln -s /usr/local/python38/bin/python3 /usr/local/bin/python3
   ln -s /usr/local/python38/bin/pip3 /usr/local/bin/pip3

   touch /etc/ld.so.conf/python38.conf
   echo "/usr/local/python38/lib" > /etc/ld.so.conf/python38.conf
   ldconfig

   =========
确认python38指向的路径是否为安装路径。某些 readline rpm安装缺少readline-devel，自行编译明明不缺少，_ctypes缺少libffi-devel包的情况可能和指令声明有关

### 运行
安装完成后首次运行时watchfile==0.18.0库报错
```
Collecting watchfiles==0.18.1
  Using cached https://files.pythonhosted.org/packages/5e/6a/2760278f309655cc7305392b0bb664738104202bf5d50396eb138258c5ca/watchfiles-0.18.1.tar.gz

  Missing build time requirements in pyproject.toml for watchfiles==0.18.1 from https://files.pythonhosted.org/packages/5e/6a/2760278f309655cc7305392b0bb664738104202bf5d50396eb138258c5ca/watchfiles-0.18.1.tar.gz#sha256=4ec0134a5e31797eb3c6c624dbe9354f2a8ee9c720e0b46fc5b7bab472b7c6d4: 'setuptools' and 'wheel'.
  This version of pip does not implement PEP 517 so it cannot build a wheel without 'setuptools' and 'wheel'.
  Could not find a version that satisfies the requirement maturin<0.14,>=0.13 (from versions: 0.7.1, 0.7.2, 0.7.6, 0.7.7, 0.7.8, 0.7.9, 0.8.0, 0.8.1, 0.8.2, 0.8.3, 0.10.0, 0.10.2, 0.10.3, 0.10.4, 0.10.5, 0.10.6, 0.11.0, 0.11.1, 0.11.2, 0.11.3, 0.11.4, 0.11.5, 0.12.0, 0.12.1, 0.12.2, 0.12.3, 0.12.4, 0.12.5, 0.12.6, 0.12.7, 0.12.8, 0.12.9, 0.12.10, 0.12.11, 0.12.12, 0.12.13, 0.12.14, 0.12.15, 0.12.16, 0.12.17)
No matching distribution found for maturin<0.14,>=0.13
You are using pip version 10.0.1, however version 23.3.1 is available.
You should consider upgrading via the 'pip install --upgrade pip' command.
```
1. 尝试更新安装pip，当前10.0更新到了23.3
   ```
   pip install --upgrade pip setuptools wheel
   ```
2. 更新pip-23.3 后 在Project Interpreter 中安装程序提示 安装文件pip xxx ，no such option --build-dir，确认是pycharm后续会修复的bug，pycharm2020以前的版本须降级到20.2.4
   ```
   python -m pip install pip==20.2.4
   ```
3. 再次安装watchfile，报错， 提示 maturin 需要 rust 编译器
   ```
   pip install watchfiles
   ---
   Collecting watchfiles==0.18.1
  Using cached watchfiles-0.18.1.tar.gz (35 kB)
  Installing build dependencies: started
  Installing build dependencies: finished with status 'error'

      DEPRECATION: The -b/--build/--build-dir/--build-directory option is deprecated. pip 20.3 will remove support for this functionality. A possible replacement is use the TMPDIR/TEMP/TMP environment variable, possibly combined with --no-clean. You can find discussion regarding this at https://github.com/pypa/pip/issues/8333.
      ERROR: Command errored out with exit status 1:
      command: /root/venv38/bin/python /root/venv38/lib/python3.8/site-packages/pip install --ignore-installed --no-user --prefix /tmp/pip-build-env-0wxi_7vm/overlay --no-warn-script-location --no-binary :none: --only-binary :none: -i https://pypi.org/simple -- 'maturin>=0.13,<0.14'
            cwd: None
      Complete output (57 lines):
      Collecting maturin<0.14,>=0.13
         Using cached maturin-0.13.7.tar.gz (154 kB)
         Installing build dependencies: started
         Installing build dependencies: finished with status 'done'
         Getting requirements to build wheel: started
         Getting requirements to build wheel: finished with status 'done'
         Preparing wheel metadata: started
         Preparing wheel metadata: finished with status 'done'
      Collecting tomli>=1.1.0; python_version < "3.11"
         Using cached tomli-2.0.1-py3-none-any.whl (12 kB)
      Building wheels for collected packages: maturin
         Building wheel for maturin (PEP 517): started
         Building wheel for maturin (PEP 517): finished with status 'error'
         ERROR: Command errored out with exit status 1:
         command: /root/venv38/bin/python /root/venv38/lib/python3.8/site-packages/pip/_vendor/pep517/_in_process.py build_wheel /tmp/tmpbyvoukdo
            cwd: /tmp/pip-install-5vztg0b7/maturin
         Complete output (36 lines):
         /tmp/pip-build-env-976vnmd1/overlay/lib/python3.8/site-packages/setuptools/config/_apply_pyprojecttoml.py:80: SetuptoolsWarning: `install_requires` overwritten in `pyproject.toml` (dependencies)
         corresp(dist, value, root_dir)
         running bdist_wheel
         running build
         running build_py
         creating build
         creating build/lib.linux-x86_64-cpython-38
         creating build/lib.linux-x86_64-cpython-38/maturin
         copying maturin/__init__.py -> build/lib.linux-x86_64-cpython-38/maturin
         copying maturin/import_hook.py -> build/lib.linux-x86_64-cpython-38/maturin
         copying maturin/__main__.py -> build/lib.linux-x86_64-cpython-38/maturin
         running egg_info
         creating maturin.egg-info
         writing maturin.egg-info/PKG-INFO
         writing dependency_links to maturin.egg-info/dependency_links.txt
         writing requirements to maturin.egg-info/requires.txt
         writing top-level names to maturin.egg-info/top_level.txt
         writing manifest file 'maturin.egg-info/SOURCES.txt'
         reading manifest file 'maturin.egg-info/SOURCES.txt'
         reading manifest template 'MANIFEST.in'
         writing manifest file 'maturin.egg-info/SOURCES.txt'
         warning: build_py: byte-compiling is disabled, skipping.

         running build_ext
         running build_rust
         error: can't find Rust compiler

         If you are using an outdated pip version, it is possible a prebuilt wheel is available for this package but pip is not able to install from it. Installing from the wheel would avoid the need for a Rust compiler.

         To update pip, run:

            pip install --upgrade pip

         and then retry package installation.

         If you did intend to build this package from source, try installing a Rust compiler from your system package manager and ensure it is on the PATH during installation. Alternatively, rustup (available at https://rustup.rs) is the recommended way to download and update the Rust compiler toolchain.
         ----------------------------------------
         ERROR: Failed building wheel for maturin
      Failed to build maturin
      ERROR: Could not build wheels for maturin which use PEP 517 and cannot be installed directly
      ----------------------------------------
      ERROR: Command errored out with exit status 1: /root/venv38/bin/python /root/venv38/lib/python3.8/site-packages/pip install --ignore-installed --no-user --prefix /tmp/pip-build-env-0wxi_7vm/overlay --no-warn-script-location --no-binary :none: --only-binary :none: -i https://pypi.org/simple -- 'maturin>=0.13,<0.14' Check the logs for full command output.
   ```
4. 单独安装 maturin, 提示需要 rust 编译器
查看rust版本 `rustc --version` 未安装
```
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```
而 rust 构建需要更新 curl 理解 "--proto"参数，该版本基于 curl-7.63.0, suse11 当前 curl 版本 7.19.7
强行执行sh https://sh.rustup.rs , 得到 rustup-init.sh 因为是网络地址下载，执行`sh rustup-init.sh` 仍然无法规避tls这个ssh协议
报错:
```
not enforcing  strong ciper  suits  for tls
```
于是放弃命令行下载更新，尝试直接下载安装包`https://static.rust-lang.org/dist/rust-1.73.0-x86_64-unknown-linux-gnu.tar.gz`完解压缩后进入目录执行 `sh install.sh` 安装，安装完成，
```
sh install.sh 
install: creating uninstall script at /usr/local/lib/rustlib/uninstall.sh
install: installing component 'rustc'
install: installing component 'rust-std-x86_64-unknown-linux-gnu'
install: installing component 'rust-docs'
install: installing component 'rust-docs-json-preview'
install: installing component 'rust-demangler-preview'
install: installing component 'cargo'
install: installing component 'rustfmt-preview'
install: installing component 'rls-preview'
install: installing component 'rust-analyzer-preview'
install: installing component 'llvm-tools-preview'
install: installing component 'clippy-preview'
install: installing component 'rust-analysis-x86_64-unknown-linux-gnu'
```
但是尝试运行，又需要GLIBC2.17 支持
```
rustc
rustc: /lib64/libpthread.so.0: version `GLIBC_2.12' not found (required by rustc)
rustc: /lib64/libc.so.6: version `GLIBC_2.14' not found (required by rustc)
rustc: /lib64/libc.so.6: version `GLIBC_2.17' not found (required by rustc)
rustc: /lib64/libc.so.6: version `GLIBC_2.14' not found (required by /usr/local/bin/../lib/librustc_driver-453cf35e1dd187fa.so)
rustc: /lib64/libc.so.6: version `GLIBC_2.16' not found (required by /usr/local/bin/../lib/librustc_driver-453cf35e1dd187fa.so)
rustc: /lib64/libpthread.so.0: version `GLIBC_2.12' not found (required by /usr/local/bin/../lib/libstd-6498d8891e016dca.so)
rustc: /lib64/libc.so.6: version `GLIBC_2.14' not found (required by /usr/local/bin/../lib/libstd-6498d8891e016dca.so)
```

5. 尝试升级 glibc
最新官方版本 glibc 2.38
当前 glibc 2.11,`ldd --version`,且`strings /lib64/libc.so.6 |grep GLIBC_`也确认只支持2.11
最新官方版本 gcc 13.2
当前 gcc 4.3.4, gcc4.8.1 才支持c11, 也有提到gcc5之后开始狂飙版本号`gcc --version`， `which gcc`得知在 /usr/bin/gcc
当前 make 3.81 `make --version`

选择gcc-4.8.5 & glibc 2.18
安装gcc-4.8.5
```c
tar zxf gcc-4.8.5.tar.gz
cd gcc-4.8.5
./contrib/download_prerequisites#执行这个文件
cd ..
mkdir gcc-4.8.5-build
cd gcc-4.8.5-build
../gcc-4.8.5/configure --prefix=/usr/local/gcc --enable-languages=c,c++ --build=x86_64-linux --disable-multilib
LD_LIBRARY_PATH="$LD_LIBRARY_PATH:/usr/local/lib"
export LD_LIBRARY_PATH 
make -j8 && make install
export PATH=/usr/local/gcc/bin:$PATH    #在当前对话中环境下，将添加进~/.bashrc中, 临时
```
安装glibc-2.18
确认需要的环境
```
oracle:/home/local/glibc-2.18 # cat INSTALL |grep -E "later|newer"
     this option if you want to compile the GNU C Library with a newer
The tests (and later installation) use some pre-existing files of the
   * GNU `make' 3.79 or newer  //make -v 3.81
   * GCC 4.4 or newer, GCC 4.6 recommended  //gcc -v  4.8.5
   * GNU `binutils' 2.20 or later //ld -v  2.24  ld是gnu binutils里的工具，是gnu的链接器
   * GNU `texinfo' 4.5 or later  //makeinfo --version  4.12, gnu的文档系统
   * GNU `sed' 3.02 or newer    //sed --version  4.15
   * GNU `gettext' 0.10.36 or later   //gettext --version 0.17
to have the header files from a 2.6.19.1 or newer kernel around for
```
如果安装2.37版本(2023)
需要的环境变更过多
```
oracle:/home/local/glibc-2.37 # cat INSTALL |grep -E "later|newer"
     this option if you want to compile the GNU C Library with a newer
     compiler flags which target a later instruction set architecture
     later.  Note that when CET is enabled, the GNU C Library requires
     Intel Pentium Pro or newer.  With '--enable-cet', it is an error to
     to build without this option (for example, if building with a newer
The tests (and later installation) use some pre-existing files of the
   * GNU 'make' 4.0 or newer
   * GCC 6.2 or newer
     building the GNU C Library, as newer compilers usually produce
     of release, this implies GCC 7.4 and newer (excepting GCC 7.5.0,
   * GNU 'binutils' 2.25 or later
     binutils 2.26 or newer.
   * GNU 'texinfo' 4.7 or later
   * GNU 'bison' 2.7 or later
   * GNU 'sed' 3.02 or newer
   * Python 3.4 or later
   * GDB 7.8 or later with support for Python 2.7/3.4 or later
   * GNU 'gettext' 0.10.36 or later
to have the header files from a 3.2 or newer kernel around for
reference.  (For the ia64 architecture, you need version 3.2.18 or newer
```
安装 texinfo 4.7
4.6版本报错
```
texinfo.txi:19: @include `versin..texi': 没有那个文件或目录.
texinfo.txi:30: Bad argument to @footnotestyle.
```
安装4.7版本
```
tar -xvf texinfo-4.7
cd texinfo-4.7
./configure --prefix=/usr/local/texinfo
export PATH=/usr/local/texinfo/bin:$PATH 
```
安装glibc 2.18，别安装在其他路径
```
tar -xvf glibc-2.18
cd glibc-2.18
mkdir build
cd build
../configure --prefix=/usr --disable-profile --enable-add-ons --with-headers=/usr/include --with-binutils=/usr/bin  
make 
make install
```

回头安装rustc, 完成后`rustc --version` 确认版本 1.73
有了可以编译 maturin 的环境，尝试 python 安装 watchfiles
完成








readline可能的报错
```
make[1]: Leaving directory `/home/data/python-gnureadline-6.2.4.1/rl/readline-6.2/doc'
test -d shlib || mkdir shlib
( cd shlib ; make  all )
make[1]: Entering directory `/home/data/python-gnureadline-6.2.4.1/rl/readline-6.2/shlib'
make[1]: Nothing to be done for `all'.
make[1]: Leaving directory `/home/data/python-gnureadline-6.2.4.1/rl/readline-6.2/shlib'
( cd examples ; make  DESTDIR= install )
make[1]: Entering directory `/home/data/python-gnureadline-6.2.4.1/rl/readline-6.2/examples'
/bin/sh ../support/mkdirs /usr/local/share/readline
mkdir /usr/local/share/readline
mkdir /usr/local/share/readline
make[1]: Leaving directory `/home/data/python-gnureadline-6.2.4.1/rl/readline-6.2/examples'
mv /usr/local/lib/libreadline.a /usr/local/lib/libreadline.old
mv: 无法获取"/usr/local/lib/libreadline.a" 的文件状态(stat): 没有那个文件或目录
make: [install-static] 错误 1 (忽略)
/usr/bin/install -c -m 644 libreadline.a /usr/local/lib/libreadline.a
test -n "ranlib" && ranlib /usr/local/lib/libreadline.a
mv /usr/local/lib/libhistory.a /usr/local/lib/libhistory.old
mv: 无法获取"/usr/local/lib/libhistory.a" 的文件状态(stat): 没有那个文件或目录
make: [install-static] 错误 1 (忽略)
/usr/bin/install -c -m 644 libhistory.a /usr/local/lib/libhistory.a
test -n "ranlib" && ranlib /usr/local/lib/libhistory.a
( cd shlib ; make  DESTDIR= install )
make[1]: Entering directory `/home/data/python-gnureadline-6.2.4.1/rl/readline-6.2/shlib'
/bin/sh ../support/mkdirs /usr/local/lib
/bin/sh ../support/mkdirs /usr/local/bin
/bin/sh ../support/shlib-install -O linux-gnu -V unknown -d /usr/local/lib -b /usr/local/bin -i "/usr/bin/install -c -m 644" libhistory.so.6.2
/bin/sh ../support/shlib-install -O linux-gnu -V unknown -d /usr/local/lib -b /usr/local/bin -i "/usr/bin/install -c -m 644" libreadline.so.6.2
install: you may need to run ldconfig
make[1]: Leaving directory `/home/data/python-gnureadline-6.2.4.1/rl/readline-6.2/shlib'
```


内网
 _curses               _curses_panel 
 _dbm                  _gdbm      
```
Python build finished successfully!
The necessary bits to build these optional modules were not found:
_bz2                      
           _hashlib           
_lzma                 _sqlite3              _ssl               
_tkinter              _uuid                 readline           
zlib                                                           
To find the necessary bits, look in setup.py in detect_modules() for the module's name.


The following modules found by detect_modules() in setup.py, have been
built by the Makefile instead, as configured by the Setup files:
_abc                  atexit                pwd                
time                                                           


Failed to build these modules:
_ctypes 
```