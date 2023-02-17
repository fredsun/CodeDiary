1. 同时打开两个项目，file -> Add Folder to WorkSpace
2. markdown实时显示preview cmd + k + v
3. 格式化 shift + option + f
4. vscode 打开terminal ctrl + shift + `
5. vscode 打开文件
   1. 先给terminal安装code命令. vscode中按下cmd+shift+p, 输入`shell`, 顶部显示 >shell, 安装"Shell Command: Install 'code' command in PATH"
   2. code 文件名  打开文件

###报错合集
1. Error: A package.json file is necessary to initialize ESLint. Run `npm init` to create a package.json file and try again.
   1. 在创建 eslint 前需要已有 package.json
1. Failed to load the ESLint library for the document
   1. npm安装分为目录安装和全局安装
      1. 全局安装`npm install -g eslint`
      2. 当前文件夹安装`npm install eslint`

1. 添加完 eslint 后报错 Parsing error: ‘＞‘ expected.
   1. 替换parser,在./eslintrc中
      1. 将`
         ```
         "parser": "@typescript-eslint/parser",
         "parserOptions": {
            "ecmaVersion": "latest",
            "sourceType": "module",
         },
         ```
      2. 替换为， 把typescript的parser添加进parserOptions中
         ```
         "parser": "vue-eslint-parser",
         "parserOptions": {
         "ecmaVersion": "latest",
            "sourceType": "module",
            "parser": "@typescript-eslint/parser",
         },
         ```
   
2. ESLint: clear(vue/comment-directive)
   1. 在./eslintrc 的 "rule"{} 中添加`"vue/comment-directive": "off"`

1. .eslintrc.js 报错第一句话 'module' is not defined
   1. .eslintrc.js 中 
      ```
      env: {
         node: true
      },
      ```

1. chrome  打开 控制台 Command + Option + J