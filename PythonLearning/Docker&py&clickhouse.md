ModuleNotFoundError：No module named ‘markupsafe’
 勾选Inherit global site-packages和Make available to all projexts
新建flask报错
1. 更新pip
   pip install --upgrade pip
2. 安装markupsafe 
   pip install markupsafe

no module named flask
 重复以上两步后执行
3. 安装flask 
   pip install flask


pip 替换国内源
linux: 
新建pip.conf, ~/.pip/pip.conf 
mkdir -p ~/.pip
touch ~/.pip/pip.conf

```
[global]
timeout = 6000
index-url = https://mirrors.aliyun.com/pypi/simple/
trusted-host = mirrors.aliyun.com
```