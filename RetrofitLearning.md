* Retrofit 是网络请求框架的封装, 后续交给okhttp
### 步骤
1. 创建接收的Bean
2. build模式创建
3. 创建Retrofit, builder创建

### 静态代理
为其他对象提供一种代理，用以控制对这个对象的访问
目的：【不关心代理过程】，为了功能维护

1. 抽象类+抽象方法
2. 目标对象继承抽象类，实现抽象方法
3. 代理类继承抽象类，实现抽象方法，其中可创建目标对象，调用抽象方法


### 动态代理
1. 无侵入【方法增强】
2. 运行时，不需要手动生成

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
1. 包含InvocationHandler对象
2. newProxyInstance(ClassLoader classLoader, Class<?>[] interfaces, InvocationHandler h) 创建代理对象
