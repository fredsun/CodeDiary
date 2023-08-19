## 第一周
### 2.13
搭建环境, 编辑 helloTypescript, helloLeaflet
学习TypeScript
### 2.14
3个helloworld合一，配置工程
尝试配置ESLint
### 2.15
完善ESLint配置
Leaflet书滚第一遍
### 2.16
补javascript基础
### 2.17
学习css
### 2.18
学习javascript.类选择器
学习火星坐标系
hellovue2

## 第二周
### 2.20
学习跨域cors
### 2.21
vue 第一次启动mars2d-vue
hello vue3
### 2.22
vue3 学习setUp函数
### 2.23
vue3 学习,firstinit
### 2.24
vue3学习teleport 解决弹窗被地图挡住的图层问题
### 2.25
leaflet 学习图层切换，组件间动态传递数值

## 第三周
### 2.27
vue3 完善teleport全局弹出的设置
### 2.28
leaflet 完成axios请求框架导入+gson解析+北京折线图 
vue3 完成typescript别名配置替换绝对路径
### 3.1
vue3 完成toast
leaflet完成点和旋转图标
第一次引入 echart

### 3.2
学习webpack

### 3.3
看完了郭佬的文章
几乎原地踏步，卡在 echart 与 leaflet 混合，
似乎是图表类本身无法与缩放绑定，geo.roam:true 不生效
百度推出的vue-echart需对vue3高度熟练，demo都暂时可读不好写


### 3.4
学习webpack

### 3.6
学习webpack和webpack-CLI

### 3.7
web开发尽量避开全局配置, 具体项目配置具体的webpack和webpack-cli
webpack卸载后查看包即可，而不是用npm webpack -v，这个是查看npm的版本

### 3.8
学习webpack

### 3.9
canvas绘折线

### 3.10
canvas绘曲线
canvas画贝塞尔曲线

### 3.11
canvas画贝塞尔曲线
手机选型，树莓派模块化

### 3.13
完成大网格的绘制，geojson

### 3.14
完成小网格的绘制，geojson
gitlfs处理大型数据
找到了清华的一个gislab的大作业，参考绘制小网格


todo处理geojson点击
todo 栅格数据，自己加载地图块儿 geoServer

### 3.15
造json假数据

### 3.16
造json假数据


### 3.17
根据Leaflet.VectorGrid源码demo里的示例，写出slice内动态设置color的方法
完成小网格图彩色ui

### 3.18
canvas通过lineDash修改线段style

### 3.20
感冒几乎无效率

### 3.21

### 3.22
canvas绘制图标

### 3.23 
~~canvas绘制图标~~
推翻，采用svg矢量图

### 3.24
老郭的小图标，使用的是字体绘制，输入a-z的ASCII码，字体库转换为特殊符号，所以地图上的符号无法改变大小

综合考虑使用svg, 
* 不会因是字体而造成字体边框留白
* 不是canvas一次性绘制，作为独立marker可以扩展，
* 可以随着地图大小默认动态变化【字体大小不变
* 气象资料开放平台key:CWB-455A46CA-A5EC-459F-80BC-00B397135036


### 3.25
ai绘制svg

### 3.27
ai绘制svg

### 3.28
ai绘制svg

### 3.29
引入element plus UI框架
引入pinia 总线控制
配置全局静态config文件
配置ts格式的store目录，类似创建全局参数类
修改alias便于使用@引入固定路径的包
新建左侧侧边栏控制

### 3.30 
潮汐地图init
geoserver>mapserver
QGIS安装
geoserver的样式legend无法preview，将jdk降低到8
天地图shp获取: 天地图"数据API" -> http://gisserver.tianditu.gov.cn/TDTService/wfs 


### 4.1
直接访问图片的方式访问瓦片
测试岚景app

### 4.3
瓦片存放规则为目录z/x/y.jpg

### 4.4
寻找tile-lnglat-transform使用方法
实则因为popup要强行显示在屏幕中，导致地图中心无法正确偏移到指定的坐标。

### 4.6
html里引入外部js，实则作为`<script>`直接引入js。
即使 npm install 后引入进了 node_modules 文件夹，但是依然无法靠import/require等方式引入，因为没有webpack打包构建。

### 4.7
初步完成assets地图zxy读取。
marker+popup+echart弹窗

### 4.8
sqlite3存储图片，将图片转化为二进制，存为blob，读取时转为base64
npm 安装 better-sqlite3 , gyp报错，删除 npm-lock.json,后重新 npm install

### 4.10
electron//类似gui桌面开发工具

### 4.11
前端数据库选型
1. indexDB 无法靠三方GUI连接，默认5MB扩展后是扩展了区域，且无法导出db，只可JSON，只可CRUD
2. sqlite3 移动端泛用，electron后期有搭配方案

* sqlite3踩坑
  * 前端引入时初步考虑直接引入sqlite3, 运行时报错缺少 gyp, 安装 npm install node-gyp 和 npm install node-pre-gyp, 后依然报错
  * 导入better-sqlite3包
  * 于是尝试本地编译sqlite3, 需py3和c++工具链环境 npm install sqlite3 --build-from-source。此时报错 ERR "aws-sdk","mock-aws-s3","nock"。
  * 于是考虑放弃sqlite3和better-sqlite3的包，使用sql-js


关于浏览器自存储的预研
1. Not allowed to load local resource
直接读取CDE盘的文件系统不可以。于是放入asset的资源路径下，完成
1. 但是文件数量过多，考虑文件格式转换后塞进数据库，即jpg图片转二进制后存为数据库blob字段
2. 基于容量、可访问的通用文件系统，考虑sqlite3


sql-wasm.js:91 Uncaught Error: incomplete input
数据库缺少字段

### 4.12
图以blob存入数据库

### 4.13
将blob导出

### 4.14
Grid继承自tileLayer，并且提供继承demo。
所以考虑重写GridLayer, 
* 注意 重写的是 `createTile: function (coords) {`，而不是` createTile: function (coords, done) {`

### 4.15
完成本地数据库读取blob字段的，并写入地图tile瓦片
### 4.16
尝试服务器形式发布 xyz瓦片
### 4.17
### 4.18
尝试运行 geoserver 运行 xyz 瓦片
### 4.19
索引优化
### 4.21
### 4.22

### 4.24
### 4.25
### 4.26
拿到点位潮汐算法的 python 文件
### 4.27


### 5.4
提交接口文档
### 5.5
尝试conda运行点位潮汐算法进 django 服务器
### 5.6
修改获取点位潮汐算法 ini 文件中 nc 的路径，可运行在 django 的 python 环境下 

### 5.9
修改vite项目适应 typescript 语法
### 5.10
打包 vite 为 dist 文件夹，塞进 django 的 pywebserver 框架
### 5.11
### 5.12
### 5.13 
本地读取潮汐点位算法接口, 初步完成 地图双击请求确认点位存在，后新建marker, 双击marker使用echart展示点位潮汐 

### 5.15

### 5.16
服务器和web项目上传svn。vscode实现git,svn同时控制前端项目

### 6.7
对进度, 初步完成左抽屉逻辑


### 7.15
打包
npm run build

 npm install -g serve
 serve -s dist


 ### 7.18 
 div 嵌套时点击事件需要防止被父类拦截，即子div设置 @click.stop，包括但不限于dialog的取消确认按钮以及dialog本身

 echart留白,optipn中设置grid` grid: {
        left: 10,
        containLabel: true,
        bottom: 10,
        top: 10,
        right: 30
    },
`

关于最高点为样式，背景symbol随过于宽的潮高number改变会很丑，切下角标号难于对齐，并且遮挡其他点位数据，于是最高点数据只显示整数，确保字符都在symbol内, 也解决了超出symbol的数字自带镂空的问题。

于是最高点markpoint在formatter中启动function，强转潮高number为int达成缩短宽度的目的


### 8.3
放弃scrollbar,recyclerview一招鲜的概念，前端更多的是随意性。
div直接包裹带v-for的 div实现有滚动条的list。
滚动条在确认chrome内核ok时，放心使用`-webkit-`格式的style
因为滚动条导致x轴边长时，hidden x 滚动条，
这一套原生组件用法>>>elementui的el-scrollbar，并且scrollTo, scrollIntoView,scrollTop之类的原生用法直接调用，而不是被scrollbar束手束脚















