##### version 3.7
##### 流程
Model->ModelLoader
Data->Decoder
Resource->Transform
TransformedResource->Transcode
TranscodedResource->封装
Target

#####
 特点：用多少加载多少
 skipMemoryCache, 默认使用内存缓存

### context获取
#### RequestManager
由 RequestManagerRetriever.get 返回
RequestManagerRetriever 的 get 方法使用的context, 如果是 Application 级, 会双重锁单例返回
private RequestManager getApplicationManager(Context context){
  if(this.applicationManager == null){
    synchronized(this){
      if(this.applicationManager == null){
        this.applicationManager = new RequestManager(context);
      }
    }
  }
  return this.applicationManager;
}


#### RequestManagerFragment
监听activity生命周期，防止无uiFragment无法控制
onStart,onStop,onDestroy.

### load
Bitmap&Gif
DrawableTypeRequest 获取到是Bitmap还是Gif

### into
Util.assertMainThread()确保是在主线程
准备好了抽象setResourse()
Target extends LifyCycleListener 便于监听
创建Request, 换绑给Target, Request被回收后放入REQUEST_POOL.offer(this)进行复用

### Request/GenericRequest
#### begin()
有指定宽高调用onSizeReady()
无指定则调用gerSize()

### target
ViewTarget.SizeDeterminer.getSize(){
  ...
  if(this.layoutListener == null){
    ViewTreeObserver observer = this.view.getViewTreeObserver();
    this.layoutListener = new ViewTarget.SizeDeterminer.SizeDeterminer;
    observer.addOnPreDrawListener(this.layoutListener);
  }
  ...
}

### ModelLoader
### DataFetcher
### Engine类
####load方法()
EngineKey用于缓存
1. loadFromCache(key, isMemoryCacheable)从 Cache 中根据 EngineKey 获取, LinkedHashMap
  * 其中 Resouce cached = this.cache.remove(key);在获取cache的同时移除了cache
  * cache获取到同时将其写入activeResources
2. loadFromActiveResources(key, isMemoryCacheable) 从正在使用的资源中查询, HashMap, 弱引用
3. EngineRunnable 获取图片(下载等) 获取后写入弱饮用缓存(active)
EngineResouce 的 acquire()记录使用次数, 不再使用的交给LruCache缓存


### 缓存
1. 内存
  1. LruCache
  2. 弱引用
2. 硬盘，防止重复访问网络
