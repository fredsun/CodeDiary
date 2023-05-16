1. 没有.gitirnore，本地 tortoise 右键一个个设置 Add to ignore list
1. svn 查看文件属性 missing 可能是因为该文件夹有另一个 .svn 控制版本，注意清除
1. 回退 
   1. 假设已提交1，2，3，4。查看log点击版本2
   2. "revert to this revision" 回到版本2，3和4的变动将被清空
   3. "revert changes from this revision" ，2的变动消失，1和3和4保留

1. vscode, git与svn并存，