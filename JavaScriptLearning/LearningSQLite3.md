### 数据类型
通过 CREATE TABLE 建表时，如果根据声明的字段类型来确定 type affinity 呢？SQLite 给出了判断规则(按顺序):
* 如果声明的类型中包含 "INT"，则为 INTEGER 
* 如果声明的类型中包含 "CHAR", "CLOB" 或 "TEXT"，则为 TEXT 
* 如果声明的类型中包含 "BLOB"，或者没有声明类型，则为 BLOB 
* 如果声明的类型中包含 "REAL", "FLOA" 或 "DOUB"，则为 REAL 其他情况，则为 NUMERIC

#### 严格类型下 只认 INT, INTEGER, REAL, TEXT, BLOB, ANY 
`CREATE TABLE <table_name> (<column_defs>...) strict`

1. hex = base 16, 编码速度快，体积大一倍
   1. 源数据的1个字节为一组, 转为2个字符表示.
1. base 64, 体积小，编码解码速度慢
   1. 源数据的3个字节为一组, 转化为4个字符表示