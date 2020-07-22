------

# 二 mysql



### 1. InnoDB的索引
InnoDB有两大类索引：
- **聚集索引(聚簇索引)** 
- **普通索引** 

>InnoDB聚集索引存储完整记录 普通索引的叶子节点存储主键值

InnoDB聚集索引的叶子节点存储行记录，因此， InnoDB必须要有，且只有一个聚集索引：
+ 1、如果表定义了PK，则PK就是聚集索引；
+ 2、如果表没有定义PK，则第一个not NULL unique列是聚集索引；
+ 3、否则，InnoDB会创建一个隐藏的row-id作为聚集索引；

### 2. 索引覆盖

>explain的输出结果Extra字段为**Using index**时，能够触发索引覆盖。
 常见的方法是：将被查询的字段，建立到联合索引里去。

![索引覆盖](./image/mysql-fugai.png) 



**哪些场景可以利用索引覆盖来优化SQL**

- 全表count查询优化：select count(name) from table ;
- 列查询回表优化：select id,name from table  where name='shenjian';
- 分页查询：select id,name from table order by name limit 500,100;


### 3. 索引原则
- 数据区分不明显的不建议创建索引
- 字段的默认值不要为 null
- 在字段上进行计算不能命中索引
- 最左前缀原则，联合索引 a,b 如果 where a ='xxx' 则不走索引
- 不要让数据库帮我们做强制类型转换
- 如果需要进行 join 的字段两表的字段类型要相同
- 负向查询不能使用索引 not in
- 前导模糊查询不能使用索引 like '%xxx'
