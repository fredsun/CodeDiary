### 结构
0. 新建项目
```
    npm create vite@latest  tidal-map  -- --template vue-ts
    cd tidal-map
    npm install
    npm run dev 
```
1. 引入
```
    --save是npm5.0时需要的，现在--save已经是内置函数了
    npm install pinia 
    npm install leaflet 
    npm install vue-router@4
    npm install pinia
    npm install element-plus
    npm install axios 
    npm install @type/node
    npm install sql-js//可读数据库，改动无法直接保存
    npm install tile-lnglat-transform//处理tile-lnglat坐标转换

    npm install echarts     
```

2. 数据库存储
    FileReader.readASDataURL(file) 读取为Base64格式

3.  geoserverserver服务器
* 安装geoserbercache插件。
  * 下载插件解压后放在`webapp`文件夹下
    * 栅格数据源
      * ImageMosaic 影响拼接，图放在一个文件夹下设置拼接。

1. tomcat服务器
   1. 用8.0的，适配jdk
   2. 修改startup.bat,解决启动闪退
      ```
      SET JAVA_HOME=D:\Program Files\java\jdk\jdk1.8.0_361
      SET TOMCAT_HOME=E:\gis_server\apache-tomcat-8.5.87
      SET CATALINA_HOME=E:\gis_server\apache-tomcat-8.5.87
      ```

