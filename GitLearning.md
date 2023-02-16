1. 添加整个文件夹
`git add 路径/`
1. git 添加多个文件, 文件名之间空格连接
`git add 文件a 文件b 文件c`
1. 提交至本地
`git commit -m "message"`
`git commit 文件名 -m "message"`跳过git add
1. 推送到远程库
完整写法`git push 远程主机名 本地分支:远程目标分支`
`git push origin dev:test`就是将本地dev分支推送到远程origin主机的test分支上
省略远程分支名`:test`,则写作`git push origin dev`
已在本地master分支时,省略本地分支名 `git push origin`


1. 查看本地分支
`git branch`
一条主分支显示
    * master
多分支如图
    branch0
    *branch1
    branch2
    master

1. git 查看远程分支
`git branch -r`
### 报错合集
1. remote: Support for password authentication was removed on August 13, 2021.
   1. fix: github2021.8.13开始强制使用personal access token
      1. Settings -> Developer settings -> Personal access tokens -> Generate new token
      2. 全选，生成一次性可见的token后输入
        `git remote set-url origin https://<yourToken>/<userName>/<repoName>`

