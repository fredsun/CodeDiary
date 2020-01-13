一、慕课网高级
- 源码
  - okhttp
    - 创建 OkhttpClient + Request 对象
    - Call call = client.newCall(request);
    - call.execute()/enqueue(new CallBack)来同步/异步请求
    - call.execute()try中 最后交给 Dispatch 做 execute()
      - OkhttpClient/Request 都由静态内部类 Builder 创建
      - Call 是接口, 实现类是 RealCall, AsyncCall 是 RealCall 的内部类, 先是 call 的 execute() 调用
      - dispatch.enqueue()异步请求中
        - 17年版
          - synchronized 锁住整个 enqueue()方法。
          - callback 封装成 AsyncCall(继承自NamedRunnable)
          - client.dispatcher.enqueue
            - 先判断是否超过最大请求数(64)&最大运行Host(5)
            - 把 runnable 放到线程池runningAsyncCalls 或 放入等待队列readyAsyncCalls
        - 19年版
          - enqueue方法分为
            - synchronized 锁住代码块 先直接加入 readyAsyncCalls队列
            - synchronized 锁住 最大请求书和最大Host的判断加入 runningAsyncCalls
          - 再锁住promoteAndExecute()中的部分
    - call.execute()try中获取 success的Response
    - call.execute()catch中 fail
    - call.execute()finally调用 dispatcher.finished

      - 同步请求中 execute
        - 17年版
          - synchronized 锁住 判断是否在同步
          - call.execute()try中 最后交给 Dispatch 做 execute()
          - call.execute()try中获取 success的Response
          - call.execute()catch中 fail
          - call.execute()finally调用 dispatcher.finished
     - finish



      - Dispatcher 核心，都是它在操作，在主线程，看作生产者
        - ExecutorService 线程池，看作消费者，synchronized锁ExecutorService对象保证线程池单例
      - 维护三个队列
        - 异步正在准备执行，等待的缓存队列 Deque<AsyncCall> readyAsyncCalls = new ArrayQueue();
        - 异步正在执行 Deque<AsyncCall> runningAsyncCalls
        - 同步请求队列 Deque<RealCall> runningSyncCalls
        - Thread.holdsLock();//判断当前线程是否持有锁 This method is designed to allow a program to assert that the current thread already holds a specified lock:
        - lock{}, 不如lock（obj）, 后者更适合用private


      - Response的获得 = getResponseWithInterceptorChain();
        - getResponseWithInterceptorChain()
          - 创建实现了 chain 的 RealInterceptorChain
          - 一个个加入 interceptor
          - chain.proceed(request)
        - 如果 RetryAndFollowUpInterceptor.isCanceled()时为成功
          - 创建 StreamAllocation 对象
          - 调用 RealInterceptorChain.proceed 递归所有拦截器进行网络请求
          - 根据异常/响应结果判断是否重新请求
          - 调用下一个拦截器，对response进行处理，返回给上一个拦截器
        - BridgeInterceptor
          - **设置header等，**
          - proceed(request)传递request, 转换接收到的response
            - 对于收到的 response 支持Gzip则直接Gzip解压
            - 判断是否含body
      - 拦截器分类
        - RetryAndFollowUpInterceptor,**进行重连**
        - BridgeInterceptor 设置header, Content-Type等,
      - Interceptor 接口
        - Response intercept()
        - 包含接口 chain
          - request()
          - proceed(Request request)



      - (Response的)Cache 缓存
        - 创建Client时创建 Cache 即可
        - 缓存用的是 DiskLruCache
        - 自带的线程池清理缓存
        - 不是get，不必要缓存
        - 写入 put
          -
          - url 经过 MD5，转为 16 进制拿到 key
          - Entry 包装缓存信息
          - DiskLruCache.editor 缓存写入
            - writeTo() 写入缓存
          - 返回 CacheRequestImpl【保存body】，该类实现CacheRequest接口
        - 读取 get
          - url 转成 key, 拿到 snapshot
          - snapshot 转成 entry
          - 判断请求和响应是否成对出现【？】
          - entry 转成 response

      - CacheInterceptor
      -   - CacheStrategy 缓存策略
      - CacheStrategy.Factory 工厂类
        - intercept()方法
          - cache 存在则尝试取出候选cache -> cacheCandidate
          - 通过 CacheStrategy.Factory 构建出CacheStrategy的参数，
            - getCandidate()
              - 无cacheResponse, 新建
              - 是https且进行过handshake()握手, 新建
              - 非可缓存的
              - 无缓存


md5加密
C++
网易云高级
动漫学校
