### Django
* pycharm新建
* python manage.py runserver  启动项目
* 项目结构
```
__init__.py 将文件夹识别为一个模块/包
asgi.py 一个asgi兼容的服务器入口
index.html
settings.py 项目的配置
urls.py 一份由django驱动的网站目录
wsgi.py 一个wsgi兼容的服务器入口
```


### 报错合计
* fail to create interpreter
  * 新建项目时BaseInterpreter设置为py3

* PermissionError: [Errno 13] Permission denied 
  * 报错权限不够，可能是路径不对，只到了文件夹，没指向文件

* You have 18 unapplied migration(s). Your project may not work properly until you apply the migrations for app(s): admin, auth, contenttypes, sessions.
Run 'python manage.py migrate' to apply them.

是将自带的数据库迁移


