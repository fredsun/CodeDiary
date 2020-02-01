* Retrofit 是网络请求框架的封装, 后续交给okhttp
### 步骤
1. 创建接收的Bean
2. build模式创建
3. 创建Retrofit, builder创建
4. 动态代理生成网络请求对象
5. 适配器将请求进行平台的适配
6. 执行器发送请求 Call
7. 数据转换器转换数据GsonConvercterFactory
8. 回到主线程处理结果

### 静态代理
为其他对象提供一种代理，用以控制对这个对象的访问
目的：【不关心代理过程】, 为了功能维护, 对原来的方法进行修改
缺点：如果类中的方法特别多, 代理类的代码量会很大

1. 抽象类+抽象方法
2. 目标对象继承抽象类，实现抽象方法
3. 代理类继承抽象类，实现抽象方法，其中可创建目标对象，调用抽象方法


### 动态代理
0. 只能代理接口
1. 无侵入【方法增强】
2. 运行时，不需要手动生成
3. 通过反射


#### JDK动态代理，只能对接口代理
#### CGLIB (修改字节码)

1. 创建接口，包含方法
2. 实现接口和方法
3. 代理类实现 InvocationHandler 接口, 实现invoke方法

InvocationHandler
1. 每一个代理类的对象都会关联一个表示内部处理逻辑的InvocationHandler接口的实现
2. invoke方法中的参数可以获取参数
3. invoke方法的返回值被返回给使用者

Proxy类
1. 包含 InvocationHandler 对象
2. newProxyInstance(ClassLoader classLoader, Class<?>[] interfaces, InvocationHandler h) 创建代理对象


#### 成员变量7个
private final Map<Method, ServiceMethod> serviceMethodCache = new LinkedHashMap();
private final Factory callFactory; //默认OkhttpClient
private final Httpurl baseUrl;
private final List<retrofit2.Converter.Factory> converterFactories; //数据转换器
private final List<retrofit2.CallAdapter.Factory> adapterFactories;
private final Executor callbackExecutor;//线程转换
private final boolean validateEagerly;

###### serviceMethodCache
先去缓存中根据method寻找缓存
没有就构建者模式创建
ServiceMethod 注解转换为对象


#### Builder中
##### 成员变量
private Platform 单例
private Factory callFactory; //默认OkhttpClient
private Httpurl baseUrl;
private List<retrofit2.Converter.Factory> converterFactories; //数据转换器
private List<retrofit2.CallAdapter.Factory> adapterFactories;
private Executor callbackExecutor;//线程转换
private boolean validateEagerly;

class Platform {
  private static final Platform = findPlatform();
  private static Platform findPlatform(){
    try{
      //反射
      Class.forName("android.os.Build");
      if(VERSION.SDK_INT != 0){
        return new Platform.Android();
      }catch(ClassNotFoundException var3){
        ;
      }
    }
  }
}

其中获取主线程的looper
new Handler(Looper.getMainLooper());

###### CallAdapter
将Call<T>转换成Java对象


###### invoke
ServiceMethod serviceMethod = Retrofit.this.loadServiceMethod();
也是Builder模式
OkHttpCall okHttpCall = new OkHttpCall(serviceMethod, arg);
return serviceMethod.callAdapter.adapt(okHttpCall);//内部调用了OkHttp的rawCall


### 同步步骤
1. ParameterHandler //解析参数/方法
2. ServiceHandler //创建Request
3. Okhttp发送网络请求 //发送网络请求
4. converter //解析返回
