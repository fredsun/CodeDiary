1. ESMAScript Version
| 1.     | ES1            | ES3                     | ES4            | ES5                                        | ES6                                                                                 | ES7 |
| ------ | -------------- | ----------------------- | -------------- | ------------------------------------------ | ----------------------------------------------------------------------------------- |
| 96年生 | 99年生，12年推 | Adobe实现，但太激进被弃 | 09年生，12年推 | 15年生，同年宣布1年1版本，正式命名为ES2015 | ES2016, 加入decorators装饰者,async-await异步模型，static class properties静态类属性 |
2. TypeScript 是 JavaScript 超集[包含, 但不局限于JS]，微软开发上
3. TypeScript=ES2016+ES2015+ES5
4. 对比
   | TypeScript                                     | JavaScript                                 |
   | ---------------------------------------------- | ------------------------------------------ |
   | JavaScript的超集，用于解决大型项目的代码复杂性 | 一种脚本语言，用于创建动态网页             |
   | 可以在编译是发现并纠正错误                     | 一种解释性语言，只能在运行时发现错误       |
   | 强类型，支持静态和动态类型                     | 弱类型，没有静态语言选项                   |
   | 最终被编译成 JavaScript 代码，被浏览器所理解   | 可以直接在浏览器中使用                     |
   | 支持模块、泛型和接口                           | 不支持模块、泛型和接口                     |
   | 支持ES3，ES4，ES5，ES6                         | 不支持编译其他ES3、ES4、ES5、ES6功能       |
   | 社区的支持仍在增长，而且还不是很大             | 大量的社区支持以及大量文档和解决问题的支持 |

5. 安装
   1. 安装 VSCode,  [官网 https://code.visualstudio.com/]
   2. 更新 Node.js , 目前 version 9.4.2[官网 https://nodejs.org/en/download/]3. 用npm命令安装TypeScript,
         `npm install typescript -g`

   3. ~~安装 Tpyings，获取.d.ts文件，当TypeScript使用外部JavaScript库时使用~~
      ~~`npm install -g typings`~~

      ~~ 执行后第一次报错~~
      ~~operation not permitted, uv_cwd,~~
      ~~重启terminal~~
      ~~第二次报错~~
      ~~执行`sudo chown -R 501:20 "/Users/fred/.npm"`~~
      ~~再执行`npm install -g typings`即可~~

   4. ~~安装 node 的 .d.ts 库~~
   ~~`typings install dt~node –global`~~
   Typings试图解决的DefinitelyTyped问题，已被加入 TypeScript 外部模块格式
   5. 直接引用js需要一定的支持，即a.js需要a.d.ts来补充类型提示支持，TSD被Typings取代，而TypeScript2.0 后 Typings 也被取代了，要获得lodash的类型文件(类型定义文件)只需要
   `npm install @types/lodash`
   `npm install @types/<pkg>`
   Typescript 2.1之后，async/await可以直接编译到ES5
   

1. 编译
`tsc helloworld.ts `
`node helloworld.js`
或者直接VSCode用F5刷新

1. 编译过程
   1. 常规语言的程序编译
      1. 文本被编译器 **解析成** abstract syntax tree[抽象句法树AST]
      2. 编译器把AST **转换为** 字节码 bytecode
   2. TypeScript编译
      1. tsc部分:
         1. TypeScript源码 -> TypeScriptAST
         2. 类型检查器 检查 TypeScriptAST
         3. TypeScriptAST -> JavaScript源码

      2. 浏览器/NodeJS/JS引擎操作:
         1. JavaScript源码 ->JavaScriptAST
         2. JavaScriptAST -> 字节码
         3. 运行字节码

1. 编译器 - VSCode
自带markdown支持，补充安装
1. Markdown All In One
1. Markdown Preview Enhanced插件
   1. 预览窗口快捷键, 分屏类是按住cmd+k，松开后再按v

1. window
报错 window is not defined / document is not defined
javascript代码可运行在 Node 和 Web 的环境中，window 是 Web环境的核心BOM的核心对象，直接在vscode运行，会找不到window

1.  第一个完整项目
    1.  mkdir TSHello && cd TSHello
    2.  npm init -y//创建默认配置，生成package.json
    3.  tsc --init//生成 tsconfig.json配置文件
         ```javascript
         {
         "declaration": true, //打包之后是否生成声明文件
         "outDir": "./dist", //js&.d.ts的输出路径
         "target":"",
         ...}
         ,
         "exclude": ["./dist"]//添加进过滤
         }
         ```

    4. `tsc `//编译项目，生成dist等, Terminal -> Run Build Task同理
    5. 运行js, cd到对应路径，运行`node xxx.js`

1. npm init
   1. 生成package.json
1. npm init -y 快速初始化
2. npm install
   1. 运行package.json 只是运行在当前文件夹  

1. npm vs npx
   1. npm一般共享package.json即可共享配置和引入
   2. npx可以本地引入同一个包的不同版本以及某个包的一部分
   3. npm 
      1. 需要的包写进 package.json
      2. 解析到包的 bin 路径
      3. bash中执行 npm run packageName
   1. npx
      1. 先判断包/命令是否在环境变量或者本地项目的 bin 目录里, 存在就直接执行
      2. 不进行包的安装，直接运行
      3. 提供包的不同版本

1. ESLint
   
   1.  `eslint --init`手动生成.eslintrc.js后自己编辑
         ```
         How would you like to use ESLint? … //一般选2
         To check syntax only //只检查语法
         To check syntax and find problems //检查语法e&&找出问题
         To check syntax, find problems, and enforce code style //检查语法&&找出问题&&强行修改格式
         What type of modules does your project use?   JavaScript modules 
         What type of modules does your project use? · esm
         Which framework does your project use? · vue
         Does your project use TypeScript? · Yes
         Where does your code run? … 
         Browser
         ``` 
      
      而后默认安装    
       eslint-plugin-vue@latest 
       @typescript-eslint/eslint-plugin@latest 
       @typescript-eslint/parser@latest eslint@latest
       生成.eslintrc.js文件


    1.`npm install eslint --save-dev`更推荐


### 报错合集
   1. Specified 'include' paths were '["**/*"]' and 'exclude' paths were '["./dist"]'
      1. 没监视到ts文件

   1. Uncaught SyntaxError SyntaxError: Cannot use import statement outside a module
      1. `import TianTuKey from "./utils-value.js"`
      2. ES6中，module为新的标准，import导入, export导出
      3. Node.js虽然实现了99%ES6的新特性，采用的是commonJS规范，使用require引入模块，使用module.exports导出接口
      4. 那么在Node.js中使用import/export方法，就通过babel的支持，使其他变成模块化代码，安装babel-jest, @babel/core, @babel/preset-env来将ES6转换为ES5
      5. 或者使用require 绕过 import,
      `let blob = require("blob-polyfill/Blob");`
      6. 直接使用`<script src = "./utils-value.js"></script>`后，使用已定义的变量