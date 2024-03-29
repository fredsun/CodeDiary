```
<!-- 开发环境版本，包含了有帮助的命令行警告 -->
<script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>
```

1. Failed to load module script: Expected a JavaScript module script but the server responded with a MIME type of "video/mp2t". Strict MIME type checking is enforced for module scripts per HTML spec.
    1. 视具体项目而定，具体项目启动顺序是
       1. npm install
       2. npm run serve//架起服务
       3. 直接点击显示的页面，而不是LiveServer或者f5刷新启动index



1. vue2 vs vue3
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

1. 安装
    ```
    //安装vue3脚手架
    npm install -g @vue/cli
    ```

1. 创建项目
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
### 语法
1. setup函数
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

1. ref函数(reference引用
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

1. vue3响应式
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

1. SFC( Single-File Component) ，单文件组件，即*.vue
   
### 动画
1. v-enter-to 在设置了 `<transition name = "xxx">`后, 动画的设置会从 `v-enter-to` 变成 `xxx-enter-to`
2. v-if是添加/删除元素, v-show是展示/隐藏元素

#### 环境变量配置
1. 配置3个文件，根目录下【src外】的


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

1. :digital envelope routines::unsupported
   1. 解决：版本问题，不回退的话，
      ```
      "scripts": {
         "serve": "set NODE_OPTIONS=--openssl-legacy-provider && vue-cli-service serve",
         "build": "vue-cli-service build"
       },
      ```


1. `defineProps()` in <script setup> cannot reference locally declared variable
   1. 非setup模式下, .vue文件中 defineProps 和 setup 属于同一级, 所以当用 defineProps 在 setup语法糖环境中定义局部变量时，会导致


1. li去掉左侧的小黑点，需要ul和li都设置 list-style: none;
```
ul{
  list-style: none;
  margin: 0;
  padding: 0;
}

/* 删除li前面的点 */
li {
  list-style: none;
  margin: 0;
  padding: 0;
}
```

1. 盒子塌陷：如果存在无法消除的间隙，即不知名的margintop效果存在。考虑父控件display的设置，如果没设置，考虑设置为flex

1. watch不生效
   1. 监听的内容确认是响应式
   2. 确认数据的变化是响应式触发，而不是直接绕开暴露的方法去修改原数据
   3. 对象类型，数组里是对象类型。数组需要最后加参数{deep:true}


1.  vue正确使用别名的方法
```
export default defineConfig({
  plugins: [vue()],
  base: "./",
		resolve: {
			alias: {
        '@': resolve(__dirname, 'src'),
      },
		},
})
```
单个vue对已经成功配置的别名引用报错时,大概率缓存问题,重启vscode.

1. echart需要固定的px宽高，而不是%
1. 函数的数组形参需要注明数组类型
1. el-date-picker 里值的格式规范化，value-format="YYYY-MM-DD HH:mm:ss" 注意yyyy大写

### 组件通信
1. 父组件调用子组件的方法 ref
``` this.$refs.childComp.play();```已被vue3废弃, 直接使用ref

1. 父组件 Main
```javascript
//获取子组件的引用，调用方法
<template>
  <Map ref="mapRef"></Map>
</template>
<script setup lang="ts">
import { ref } from 'vue'
const mapRef = ref();
  mapRef.value.moveToFocusPoint(point);
</script>
```

1. 子组件 Map
```javascript
<script setup lang="ts">
function moveToFocusPoint(point: Point) {

    let center = L.latLng(point.lat, point.lng);

    map.setView(center, 8);
}

defineExpose({
    moveToFocusPoint
})
</script>
```

1. box-shadow需要设置` box-shadow:0 1px 2px rgba(60,64,67,0.3), 0 2px 6px 2px rgba(60,64,67,0.15);`和`  z-index: 2;`才生效, 如果被同级元素遮挡, 考虑自身设置`position:relative`

1. overflow:auto，类似事件传递机制，得注意外部是否也会拦截页面滑动
