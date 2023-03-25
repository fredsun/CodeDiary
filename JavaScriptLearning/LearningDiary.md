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


