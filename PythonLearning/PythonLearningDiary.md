<!--
 * @Author: fredsun fredsun.me@gmail.com
 * @Date: 2023-09-01 17:07:43
 * @LastEditors: fredsun fredsun.me@gmail.com
 * @LastEditTime: 2023-09-25 15:08:34
 * @FilePath: \CodeDiary\PythonLearning\PythonLearningDiary.md
 * @Description: 
 * 
 * Copyright (c) 2023 by ${git_name_email}, All Rights Reserved. 
-->
<!--
 * @Author: fredsun fredsun.me@gmail.com
 * @Date: 2023-09-01 17:07:43
 * @LastEditors: fredsun fredsun.me@gmail.com
 * @LastEditTime: 2023-09-01 20:30:02
 * @FilePath: \CodeDiary\PythonLearning\PythonLearningDiary.md
 * @Description: 
 * 
 * Copyright (c) 2023 by ${git_name_email}, All Rights Reserved. 
-->

## 项目迁移后 环境变量修改
1. 打开项目后报错 cannot set up a python sdk at python 3.12
File - settings - Project:xxx - python Interpreter 中无法install tools, 
解决：修改 pyvenv.cfg , 项目根目录一处中 python 路径为本地 python

1. 迁移项目后，需修改多处环境变量
2.1 分别修改
venv/script/activate 中的 VIRTUAL_ENV 两处为项目的venv
venv/script/activate.bat 中的 VIRTUAL_ENV 一处为项目的venv
参考:https://blog.csdn.net/PSpiritV/article/details/122993602

1. ModuleNotFoundError: No module named 'PyQt5.sip'
```
python -m pip install --upgrade --force-reinstall pip
python -m pip install --upgrade --force-reinstall PyQt5
```

解决：只能分别 强行卸载并且重新安装 sip，pyqt5。
错误尝试：
`pip install pyqt5.sip`
但是会报错 Fatal error in launcher: Unable to create process using '"D:\SVN_Projects\wimodel\venv\Scripts\python.exe"  "E:\code_fan\wimodel\venv\Scripts\pip.exe" install pyqt5.sip': ???????????  。
原因是环境变量没同步干净，第一个为原环境变量，这里直接强行卸载 sip 并重装
`python -m pip install --upgrade --force-reinstall pip`
再次尝试  `pip install pyqt5.sip  `
返回:Requirement already satisfied: pyqt5.sip in e:\code_fan\wimodel\venv\lib\site-packages (12.12.2)
于是尝试卸载重装pyqt5
```
python -m pip install --upgrade --force-reinstall PyQt5
```
成功导入PyQt5.sip
以上再次参考:https://blog.csdn.net/PSpiritV/article/details/122993602



## 安装pip依赖

### 别随意sudo
sudo pip3 install xxxxx 会调用系统级的 pip3/pip。导致安装到系统site_packages 
项目虚拟环境下安装依赖，pip install xxxx即可 


## 清华源容易报错host
切阿里源 http://mirrors.aliyun.com/
## 打包
pip3 install pyinstaller
pyinstaller -F -w .\recv_mirror.py --noconsole 

## Error loading Python DLL xxx python39.dll，LoadLibrary: PyInstaller: FormatMessageW failed 
win7不支持python3.9，降级到3.8即可


## PyUIC 踩坑
PyUIC对py文件也可转换，且转换后直接生成新文件，无法 ctrl + z 复原，可从右键‘local history’中恢复
