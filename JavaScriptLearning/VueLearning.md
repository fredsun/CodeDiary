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

2. 创建项目
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