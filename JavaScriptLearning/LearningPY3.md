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


#### 新建接口

* 新建app，<kbd>tools</kbd> - <kbd>run manage.py Task...</kbd> 执行 `start appName`  
* 回到根文件夹 settings.py, `INCLUDE_APPS`补充新建的app,`'appName',`
* 回到根文件夹utrls.py中分配路由`url_patterns`配置`path('urlName/',include('appName.urls')),`


* 在 appName 文件夹下新建 urls.py
  ```python
  #!/usr/bin/env python
  # -*- coding:utf-8 -*-  
  __author__ = 'fredsun'
  __time__ = '2023-04-26 13:31'
  from django.urls import path,include
  from . import views
  urlpatterns = [

  ]
  ```

* 在 appName文件夹下的views.py写服务
  ```python
  from django.shortcuts import render
  from django.http import HttpResponse
  # Create your views here.

  def index(request):
      print('我是路径',request.path)
      print('我是方法',request.method)
      print('我是get',request.GET)
      print('我是post',request.POST)
      return HttpResponse('小叮当在测试request对象')
  ```

* 在appName文件夹下的urls.py也补充pattern
  ```python
  urlpatterns=[
    path('index', views.index)
  ]
  ```

#### get请求
* 在 appName 文件夹下的 views.py 写服务
  ```python
  def index(request):
      print('我是主页')
      return render(request,'get_post_test/get_post.html')

  def appName(request):
      print('我是get视图')
      print('方法',request.method)
      a = request.GET.get('a')
      b = request.GET.get('b')
      print(a,b)
      return HttpResponse('小叮当在测试get')
  ```
* 在 urls.py 中配置路由
  ```python
  #!/usr/bin/env python
  # -*- coding:utf-8 -*-
  __author__ = 'IT小叮当'
  __time__ = '2019-02-26 15:54'
  from django.urls import path,include
  from . import views
  urlpatterns = [
  path('index',views.index),
  path('appName',views.appName,name='appName'),
  ]
  ```
* 请求格式`http://127.0.0.1:8000/appName/index?a=1&b=2`


###  是否重复插入数据
* 通过唯一索引是否存在判断，将x,y,z作为联合索引设置为唯一索引
* 或者先判断再插入


### Flask
老师给的项目，没有运行的configuration
自行安装flash和pywebview
```
 pip install pywebview  
 pip install flask  
 python main.py
```
使用 python 命令运行的 main.py 文件，而不是使用 PyCharm 的运行按钮，否则无法执行`if __name__ == '__main__':`后的内容
因为
当命令行执行时，__name__是__main__，
当直接点击 Ptcharm执行时，__name__ 是 app

### 报错合计
* fail to create interpreter
  * 新建项目时BaseInterpreter设置为py3

* PermissionError: [Errno 13] Permission denied 
  * 报错权限不够，可能是路径不对，只到了文件夹，没指向文件

* You have 18 unapplied migration(s). Your project may not work properly until you apply the migrations for app(s): admin, auth, contenttypes, sessions.
Run 'python manage.py migrate' to apply them.

是将自带的数据库迁移

* unrecognized token:
  * 数据库查询注意引号等符号


* UnicodeDecodeError: 'gbk' codec can't decode byte 0xaf in position 183: illegal multibyte sequence
  * 读取文件时，文件编码不用 win 默认，改为 utf-8 

* do not use bare except
  * 具体的异常 `except XXX as e:`


* AssertionError: View function mapping is overwriting an existing endpoint function: index
  * flash里def的函数名有重复的，比如index

* IndentationError:unindent does not match any outer indentation level
  * 检查拼写

* ModuleNotFoundError: No module named 'webview'
  * pip install pywebview

* has been blocked by CORS policy: The value of the 'Access-Control-Allow-Origin' header in the response must not be the wildcard '*' when the request's credentials mode is 'include'. The credentials mode of requests initiated by the XMLHttpRequest is controlled by the withCredentials attribute.
  * 服务器未开启CORS.
  * `  response["Access-Control-Allow-Origin"] = "*"`修改为`  response.headers.["Access-Control-Allow-Origin"] = ""`

* 服务器返回字段直接 JsonResponse 处理拼接的data，dumps会生成 JSON字符串，导致 " 被转义