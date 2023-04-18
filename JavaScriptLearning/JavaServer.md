
### 报错
* java: 程序包sun.misc不存在
  * 降低jdk为1.8
* java: 程序包com.jeesite.common.mybatis不存在
  * rebuild
* 控制台输出乱码
  * settings - editor - general - console - Default Encoding 改Utf-8

* java.lang.ClassNotFoundException: org.sqlite.JDBC
  * jdbc的jar包没加入，或者没依赖进整个项目
    * 查看lib或者jar目录下的jdbc.jar左侧是否有箭头，没有的话右键选择add as library
    * ProjectStructore - Module - 完整项目的 Dependencies 勾选jdbc.jar
    * ProjectStructore - Artifacts - Output Layout 下是否已经加入了右侧Available Elements中的jdbc.jar


* java.sql.SQLFeatureNotSupportedException: not implemented by SQLite JDBC driver
  * jdbc不支持这种数据库读取方法，`star.executeQuery`替换为