### 报错
1. `Unable to render rich display`,
   或 `batch response: This repository is over its data quota. Account responsible for LFS bandwidth should`
2. github存储文件超过额定大小时，大型文件会被gitlfs代为存储。直接下载/pull时github仓库里原大文件的文件名和格式不会变，但是内容会变成一段gitlfs地址和sha1.
   1. 如下`version https://git-lfs.github.com/spec/v1 oid sha256:465d01ae78defcfa05f77d8acf243bccb62693563be94311ba799b6b249660e9 size 20687727`
3. 想要获取完整大小的原文件，需要将原项目fork后进入Settings-Archives，勾选Include Git LFS objects in archives，并通过rar下载。
   1. 参考[csdn](https://blog.csdn.net/xll_bit/article/details/124498421)
   2. 原文件
    ![https://raw.githubusercontent.com/fredsun/RES/master/gitlfs-largefile1.png]
    3. 查看直接下载后的, 和原19MB相比，只有1KB
    ！[https://raw.githubusercontent.com/fredsun/RES/master/gitlfs-largefile2.png]
    4. fork后进入fork的项目settings
    ![https://raw.githubusercontent.com/fredsun/RES/master/gitlfs-largefile3.png]
    5. Archives设置包含LFS
    !(https://raw.githubusercontent.com/fredsun/RES/master/gitlfs-largefile4.png)[]

