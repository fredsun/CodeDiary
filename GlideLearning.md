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
