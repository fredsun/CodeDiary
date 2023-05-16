### 结构
* .env，根目录下设置 环境， baseURL 等变量


### 操作
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
    npm install sql-js //可读数据库，改动无法直接保存
    npm install tile-lnglat-transform //处理tile-lnglat坐标转换
    npm install n-progress //请求时顶部加载进度条
    npm i pinia-plugin-persistedstate//pinia持久化
    npm install echarts    
```

2. 数据库存储
    FileReader.readASDataURL(file) 读取为 Base64 格式

3. ~~geoserverserver 服务器(java)~~
    * 安装 geoserbercache 插件。
      * 下载插件解压后放在`webapp`文件夹下
        * 栅格数据源
          * ImageMosaic 影响拼接，图放在一个文件夹下设置拼接。

4. ~~tomcat 服务器~~
   1. 用8.0的，适配jdk
   2. 修改startup.bat,解决启动闪退
      ```
      SET JAVA_HOME=D:\Program Files\java\jdk\jdk1.8.0_361
      SET TOMCAT_HOME=E:\gis_server\apache-tomcat-8.5.87
      SET CATALINA_HOME=E:\gis_server\apache-tomcat-8.5.87
      ```

5. python/django服务器


2. configuration -> Environment -> Environment variables: 设置为 `PYTHONUNBUFFERED=1;DJANGO_SETTINGS_MODULE=TidalMapDjangoProject.settings`
3. python intercepter 请创建新的虚拟环境，版本为 python3.9
4. 需要导入的包，
    ```
    pip install Django
    pip install pywebview
    ```
5. 需要将 tide-svr 中除 'load_tide', 'ocean_tide', 'data', fes2014' 外的全部.dll, lib, exp, pyd文件放在根目录 venv 路径下确保程序可以被识别, venv本身为临时生成, 无法上传


### 潮汐点逻辑整理
1. 单点接口设计4个参数。
   * lng,  经度
   * lat, 纬度
   * date_bj, 北京时间制的时间
   * interval_minutes 潮汐间隔

2. 双击地图生成 marker，点击 marker 展示该点位潮汐数据，默认一个日期+24h制
3. marker 可以做 pin 固定，固定后变色，
4. marker 可以批量查询，框选 marker，设置查询时间和间隔。批量获取数据
5. marker 设置放大按钮，点击后，map最小化到右侧，popupwindow弹出

