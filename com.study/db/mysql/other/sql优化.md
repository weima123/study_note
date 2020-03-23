## 一.索引
### 1.索引概念：
```
索引是帮助存储引擎更快的找到记录的一种数据结构
```
### 2.索引的缺点：
```
1)索引会占用磁盘或者内存空间
2）减缓了数据写入（新增、更新、删除）的时间
```
## 二.索引的类型
### 1.索引的类型
```
大类
1）.普通索引
2）.唯一索引
3）.联合索引--最左前缀原则
4）.主键索引
结构
1）hash索引
2）BTree索引
```
### 2.hash索引、Btree索引的区别
```
 hash索引的实现-哈希表
 btree索引的实现--B树
 一般情况下hash索引的索引效率较btree高
1） hash索引只能用于= 、IN 、<=>的查询，无法用于范围查询
2)hash索引不能用于排序：hash索引因为存放的是经过运算后的hash值，hash值的大小关系不一定和运算前的一致，所以hash索引不能用于排序
3）hash索引不能利用部分索引键查询：hash类型的组合索引，是将组合索引数据合并后再计算的hash值而不是单独运算的hash值，因此不能利用部分索引
键查询
4）hash索引任何时候都不能避免扫描表
5）hash索引遇到大量hash相等的数据，不一定就会比BTree快
```

### 3.联合索引的构造原则--最左前缀原则
```
1.经常使用的列优先
2.选择性高的列优先
3.宽度小的列优先
```

## 三.explain
### 1.常用列的含义
字段名称|含义|取值
---|---|---
type|数据引擎查找表的方式|const、ref_eq、ref、range、index、all
possibleKeys|可能用到的索引|
key|实际用到的索引|
rows|扫描的数据行数|

### 2.type说明
```
const 常量值
ref_eq 使用了索引并且是主键索引或者唯一索引，结果集唯一
ref 使用了索引但不是主键索引或者唯一索引，结果集不唯一
range 使用了某个索引的部分索引，属于范围查找，between in or > < >= <= 
index 使用了索引，进行了全表扫描，因为索引文件比数据文件小，因此效果好于all
all 未使用索引，全表扫描
```

## 四.索引失效的场景(login字段存在索引、language字段不存在索引）
### 1.列类型是字符串，未使用引号
```
使用索引
EXPLAIN SELECT * FROM art_user WHERE login = '13900000002' ;
不使用索引：
EXPLAIN SELECT * FROM art_user WHERE login = 13900000002 ;
```

### 2.使用like,通配符位于左边
```
不使用索引：
EXPLAIN SELECT * FROM art_user WHERE login LIKE '%13900000002'
不使用索引：
EXPLAIN SELECT * FROM art_user WHERE login LIKE '%13900000002%'
使用索引：
EXPLAIN SELECT * FROM art_user WHERE login LIKE '13900000002%'

```
### 3.使用or条件会导致索引失效，除非给or的每列都加上索引条件
```
不使用索引：
EXPLAIN SELECT * FROM art_user WHERE login = '13900000002'  or language = 'zh_CN';
使用索引：language列也存在索引的情况下，会使用索引
```
### 4.对索引列进行函数运算
````
不使用索引：
EXPLAIN SELECT * FROM art_user WHERE MID(login,0,11) = '13900000002'

````
### 5.联合索引失效的场景

```
例如联合索引有三个索引字段（A,B,C）

查询条件：

（A，，）---会使用索引

（A，B，）---会使用索引

（A，B，C）---会使用索引

（，B，C）---不会使用索引

（，，C）---不会使用索引

```
## 五.索引的实现
### 1.mysql在存储引擎服务端并不会实现索引，索引的实现是交给存储引擎去实现的，不同的存储引擎支持不同类型的索引
### 2.INNodB 、MyISAM都支持B/B+树索引
### 3.聚集索引、非聚集索引
```
1.非聚集索引：索引文件和数据文件分开存储(MYISAM)
2.聚集索引：索引文件和数据文件存储在一起（INNODB)
```

