1. divIcon 自定义图标
2. featureGroup 特征组，选择给定一组初始层和一个options对象
3. geoJSON一个/一组GeoJson对象
4. marker 可拖动图标
5. polyline 折线
6. 坐标系
   1. WGS84，国际通用地球坐标系
      1. GoogleMap除中国, ArcGIS, OpenStreetMap, 
   2. CGS2000
      1. 天地图
   3. GCJ02，火星坐标
      1. 谷歌中国，高德，腾讯
   1. BD09
      1. 百度
7. 天地图是无偏移的坐标系，CGCS2000 
8. 百度地图是经过国标GCJ002加密成火星地图后继续二次加密的bd09
9. 自定义icon会随着地图缩放，坐标发生改变，iconAnchor无法解决，尝试Leaflet.Canvas-Markers
10. leaflet不包含canvas引入, canvas本身是html标签，leaflet原生提供的是map的绘制使用canvas或控件的的绘制使用canvas，没有直接的canvasLayer.考虑使用风场等demo都依赖的glayers（原名L.canvasLayers）绘制

### 源码相关
1. map中一层layers是
   1. 普通layers
   2. popupwindow的点
   3. popupwindow弹出的气泡
   4. 空的

### 报错合集
1.  marker漂移
    1.  设置iconAnchor为图标size的一半
