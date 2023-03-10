```
<!-- 开发环境版本，包含了有帮助的命令行警告 -->
<script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>
```

1. Failed to load module script: Expected a JavaScript module script but the server responded with a MIME type of "video/mp2t". Strict MIME type checking is enforced for module scripts per HTML spec.
    1. 视具体项目而定，具体项目启动顺序是
       1. npm install
       2. npm run serve//架起服务
       3. 直接点击显示的页面，而不是LiveServer或者f5刷新启动index



2. vue2 vs vue3
   1. compositionAPI
      1. setUp配置
      2. ref与reactive
      3. watch与watchEffect
      4. provide与inject
   2. Fragment
   3. Teleport
   4. Suspense
   5. 新的生命周期钩子
   6. data对象始终被声明为一个函数
   7. 移除keycode支持作为v-on的修饰符 

3. 安装
    ```
    //安装vue3脚手架
    npm install -g @vue/cli
    ```

4. 创建项目
   1. vue-cli
    ```
    vue create vue3_test
    cd vue3_test
    npm run serve
    ```
    2. vite//无需打包+HMR热重载+按需编译
    ```
    npm init vite-app <project-name>
    cd <project-name>
    npm install
    npm run dev 
    ```

5. setup函数
   1. vue3配置项
   2. 返回值是
      1. 对象，则对象值可直接用
      2. 渲染函数，即return对象
      3. vue2可访问到setUp中的对象，set不可访问setUp中的
   3. 执行周期比beforeCreate早，this是undefined
   4. 外部的事件触发需要在内部设置emits声明
   5. 参数
      1. props, 值为对象，组件外部传递过来，且组件内部声明了接受的属性
      2. context，上下文对象
         1. attrs，值为对象，组件外部传递，但没有在props配置中声明属性，相当于this.$attrs
         2. slots，收到插槽内容，相当于this.$slots, v-slot:qwe
         3. emit，分发自定义事件的函数，相当于this.$emit，父组件绑定的 hello 事件，子组件需要在exports中设置 emits:['hello'],

6. ref函数(reference引用
   1. 定义基本数据类型，对象靠reactive函数转化为Proxy来操作
   2. 操作数据需要.value, 读取数据不需要.value
   3. 将输入转为RefImpl对象 RefImpl{...}
   4. ref函数的原型里包含getter和setter,相当于vue2的_data, 数据劫持才能实现响应式
   5. 如果ref()里包含对象类型，求助了vue3的reactive函数(封装了ES6的Proxy)


1. reactive函数
   1. 处理对象和数组。基本数据转化为对象存储
   2. 操作读取数据均不需要.value
   3. 转化为proxy对象
   4. reactive把源对象转化为代理对象，操作代理对象
      ```
      let person = reactive({
         name:'张三',
         age:18,
         job:{
            type:'aa',
            salary:'bbb',
            a:{
               b:{
                  c:1,
               }
            }
         }
      })
      ```

2. vue3响应式
   1. vue2中
      1. 对象类型，通过Object.defineProperty()对属性进行读取、修改和拦截（数据劫持）
      2. 数组类型，通过重写数组的更新方法来拦截，
      3. 缺陷：
         1. 新增/删除数组时界面，不会更新，
            1. 解决方案
               ```javascript
               this.$set(this.person, 'name','aaa') 
               或Vue.set(this.person, 'name','aaa') 
               ```
         2. 下标修改数组，界面不会更新
               ```javascript
               this.$set(this.person, 'name','aaa') 
               或Vue.set(this.person, 'name','aaa') 
               //或者直接更新数据
               this.person.name.splice(0,  1, bbb)
               ```
   2. Proxy代理
      1. 增删改查靠handler 
      ```javascript
      const p = new Proxy(person,{
         get(target, propName){
            return target[propName];
         }
         //增也在这
         set(target, propName, value){
            target[propName] = value;
         }
         delete(target, propName){
            turn delete target[propName];
         }
      })
      ```
   3.  通过Reflect反射
       1. 对源对象的属性操作
       2. 减少try catch 

1. computed函数
   ```javascript
   import{computed} from 'vue'
   setup(){
      //简写, 只考虑只读
      let fullName = computed(()=>{
         return person.firstName + '-' + person.lastName;
      })
      //完整，考虑读和写
      let fullName = computed(()=>{
         get(){
            return person.firstName + '-' + person.lastName;
         },
         set(){
            const nameAttr = value.split('-')
            person.firstName = nameAttr[0]
            person.lastName = nameAttr[1]
         }
      })
   }
   ```
1. 监视
   1. watch函数
      1. 与vue2类似
      2. 不要使用箭头函数，因为指向当前作用域
         1. 监视reactive
            1. 无法正确获取oldValue
            2. 强制开启深度监视
            3. 监视对象，则强制开启deep监视
            4. 对象中的属性（非对象），depp监视开关可选 
         2. 监视ref


   1. watchEffect
      1. watch既要指明属性，又要指明监视回调
      2. watchEffect不用指明监视哪个属性，监控的回调中的用到哪个属性，就监视哪个
         1. 类似computed, 
            1. computed 注重结果
            2. watchEffect 注重过程

1. 生命周期
   1. beforeCreated; 相当于setUp()
   2. created; 相当于setUp()
   3. beforeMounted;
   4. mounted;
   5. beforeUnMounted;
   6. unmounted


1. 自定义hooks
   ```javascript
   const point = usePoint()
   ```
   1. 把 setUp 函数中使用 CompositionAPI 的函数进行封装
   2. 类似vue2中的mixin

1. toRef()
   1. 创建一个ref对象，value指向另一个ref中对象中的某个属性
      1. ref()会生成新的ref对象 
   ```
   const name = toRef(person,'name')
   ```
   1. 用处：要将要响应式对象中的某个属性给外部使用时
   2. toRefs() 和 toRef()功能相同，批量处理

 1. shallowRef()
   1. 只处理基本数据类型，对象以及是object，不动。
      1. 对象的后续不会修改其中的属性，用这个方法

1. shallReactive()
   1. 只处理对象最外层的响应式，浅响应式，
      1. 对象数据深，但只处理外层属性

1. readOnly
   1.  深只读
2.  shallowReadOnly
    1.  浅只读

1. toRaw 转原始数据
2. markRaw 永远不会成为响应式对象，只会是最原始数据，渲染大数据
3. customRef 自定义响应式，并对其依赖和触发控制

1. 实现父与后代组件通信
   1. provide
   2. inject

1. 响应式组件判断
   1. isRef
   2. isReactive
   3. isReadonly
   4. isProxy 是否由rective/readonly创建的

1. app的配置需要在mount前
2. axios访问本地文件404
   1. 需要在public文件夹下新建js文件夹后放置

3. fragment是元素外自带的外套，虚拟的元素
4. Teleport 用to传递数据到任意层/最外层
5. inject 直接写入setup中，别写在监听里
6. Suspense 异步渲染
   1. 
7. 插值写法


### 报错
1. 无法加载文件 D:\Program Files\nodejs\node_global\vue.ps1，因为在此系统上禁止运行脚本。有关详细信息，请参阅 https:/go.micros oft.com
   1. win默认禁止了脚本运行
   2. 解决：
   ```
   //查看禁止状态，Restricted表示禁止
   get-ExecutionPolicy
   set-ExecutionPolicy RemoteSigned 
   Y
   ```
1.   Uncaught SyntaxError: Unexpected identifier 'xxx'
     1.   检查,号""号