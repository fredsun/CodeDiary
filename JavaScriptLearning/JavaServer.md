##### 加载速度慢
*  184w条数据，4.90GB的图片，增加3个字段后得到6.06GB的sqlite数据库
* 查询 6.06GB，直接sql执行时间约 2000-4000 毫秒
* 数据库上xyz字段索引，sql查询时间50毫秒，sqlite大小增大至 6.10GB
* 本次基于多个列，是组合索引
索引适用数据量大即过万且增删改查少的表中
* 原两级缩放的数据库查询一条数据需100毫秒【未使用索引】

##### 数据库调用完记得全部关闭
resultSet.close();
statement.close();
connection.close();

尽管connection.close会实现statement.close和resultSet.close，但是不建议这么做，因为connection.close会关闭整个数据库，而statement.close和resultSet.close只是关闭当前的statement和resultSet，不会影响其他的statement和resultSet。当调用连接池的时候，connection.close会把连接归还给连接池，而statement.close和resultSet.close不会。
### 报错
* java: 程序包sun.misc不存在
  * 降低jdk为1.8
* java: 程序包com.jeesite.common.mybatis不存在
  * rebuild
* 控制台输出乱码
  * settings - editor - general - console - Default Encoding 改Utf-8

* servlet404,
  * 考虑权限
  * 
* java.lang.ClassNotFoundException: org.sqlite.JDBC
  * jdbc的jar包没加入，或者没依赖进整个项目
    * 查看lib或者jar目录下的jdbc.jar左侧是否有箭头，没有的话右键选择add as library
    * ProjectStructore - Module - 完整项目的 Dependencies 勾选jdbc.jar
    * ProjectStructore - Artifacts - Output Layout 下是否已经加入了右侧Available Elements中的jdbc.jar


* java.sql.SQLFeatureNotSupportedException: not implemented by SQLite JDBC driver
  * jdbc不支持这种数据库读取方法，blob字段存取是setBytes()/getBytes()

