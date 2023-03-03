1. wcf服务，安装时直接搜索wcf，或安装后在工具-获取工具和功能处 加装
   1. 本次echart使用的是.net4.7.1
   2. 参考：https://blog.csdn.net/weixin_45706856/article/details/123439278
2. 引发的异常:“System.ServiceModel.AddressAccessDeniedException”(位于 System.ServiceModel.dll 中
   1. 端口的调用权限不够.
   2. 解决方案: 管理员模式运行整个vs
    ```
    //错误写法
    netsh http add urlacl url= http://+:8259/AccountService/ user="\Everyone"
    //正确写法
    netsh http add urlacl url= http://+:7789/ user="\fredsunwin"
    netsh http add urlacl url= http://+:7789/ user="\Everyone"
    //查看是否添加成功
    netsh http show urlacl
    ```
    3. 参考：https://www.cnblogs.com/abccome/p/4395420.html

3. 引发的异常:“System.Runtime.InteropServices.COMException”(位于 DEMO.exe 中)
   1. 路径错误，临时使用绝对路径