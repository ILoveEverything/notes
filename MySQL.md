# `MySQL`常用命令

## 数据库服务操作

- 查看数据库服务状态：`sudo service mysql status`
- 开启数据库服务：`sudo service mysql start`
- 停止数据库服务：`sudo service mysql stop`
- 重启数据库服务：`sudo service mysql restart`

## 数据库登陆和退出

- 登陆数据库：`mysql -u用户名 -p密码`
- 退出数据库：`exit` 或` quit` 或` ctrl+c`

## 数据库操作

- 查看所有数据库：`show databases;`
- 查看创建数据库`SQL`语句：`show create database 数据库名;`
- 创建数据库：`create database 数据库名 charset=编码格式（utf8);`
- 使用数据库：`use 数据库名;`
- 查看当前使用的数据库：`select database();`
- 删除数据库：`drop database 数据库名;`

## 数据表操作

- 查看当前数据库中的所有表：`show tables;`
- 查看创建数据表`SQL`语句：`show create table 表名;`
- 创建表：`create table 表名(字段名称 数据类型 可选的约束条件)；`	
  - `unsigned`:无符号
  - `primary key`:主键
  - `auto_increment`:递增
  - `not null`:非空
- 查看表结构：`desc 表名;`
- 修改表-添加字段：`alter table 表名 add 字段名称 数据类型 约束条件;`
- 修改表-修改字段类型或约束条件：`alter table 表名 modify 字段名称 数据类型 约束条件;`
- 修改表-修改字段名称和字段类型或约束条件：`alter table 表名 change 旧字段名称 新字段名称 数据类型 约束条件;`
- 修改表-删除字段：`alter table 表名 drop 字段名称;`
- 删除表：`drop table 表名;`

## 数据增删改查

### 查询

- 查询数据-查询所有列：`select * from 表名;`
- 查询数据-查询指定列：`select 列1,列2,... from 表名;`
- `distinct`可以去除重复数据行：`select distinct name, gender from students;`

### 添加

- 添加数据-全列插入：`insert into 表名 values (...);`
- 添加数据-部分列插入：`insert into 表名 (列1,...) values(值1,...);`
- 添加数据-全列多行插入：`insert into 表名 values(...),(...)...;`
- 添加数据-部分列多行插入：`insert into 表名 (列1,...) values(值1,...),(值1,...)...;`

### 修改

- 修改数据：`update 表名 set 列1=值1,列2=值2... where 条件;`

### 删除

- 删除数据：`delete from 表名 where 条件;`

## `as`关键字

- 使用 as 给字段和表起别名：`select id as 序号, name as 名字, gender as 性别 from students as s;`
  

## 排序

- 排序：`select * from 表名 order by 列1 asc|desc [,列2 asc|desc,...]`

**语法说明:**

1. 先按照列1进行排序，如果列1的值相同时，则按照 列2 排序，以此类推
2. `asc`从小到大排列，即升序
3. `desc`从大到小排序，即降序
4. 默认按照列值从小到大排列（即`asc`关键字）

## 分页查询

`select * from 表名 limit start,count`

**说明:**

1. limit是分页查询关键字
2. start表示开始行索引，默认是0
3. count表示查询条数

## 聚合函数
- `count(col)`: 表示求指定列的总行数
- `max(col)`: 表示求指定列的最大值
- `min(col)`: 表示求指定列的最小值
- `sum(col)`: 表示求指定列的和
- `avg(col)`: 表示求指定列的平均值
- `select avg(ifnull(height,0)) from students where gender =1;`
- `ifnull`函数: 表示判断指定字段的值是否为null，如果为空使用自己提供的值。

## 分组查询

- 根据gender字段来分组

  `select gender from students group by gender;`

- 根据name和gender字段进行分组
  	`select name, gender from students group by name, gender;`

- 根据gender字段进行分组， 查询gender字段和分组的name字段信息
  	
`select gender,group_concat(name) from students group by gender; `
  	
- 统计不同性别的人的平均年龄
  	`select gender,avg(age) from students group by gender;`

- 统计不同性别的人的个数
  	`select gender,count(*) from students group by gender;`*

- 根据gender字段进行分组，统计分组条数大于2的
  	`select gender,count(*) from students group by gender having count(*)>2;`

- 根据gender字段进行分组，汇总总人数
  	`select gender,count(*) from students group by gender with rollup;`

- 根据gender字段进行分组，汇总所有人的年龄
  	`select gender,group_concat(age) from students group by gender with rollup;`

## 连表查询

- 内连接查询
  		`select 字段 from 表1 inner join 表2 on 表1.字段1 = 表2.字段2;`
- 左连接查询
  		`select 字段 from 表1 left join 表2 on 表1.字段1 = 表2.字段2;`
- 右连接查询
  		`select 字段 from 表1 right join 表2 on 表1.字段1 = 表2.字段2;`

## 执行`sql`文件		
​	`source areas.sql;`

## 添加外键约束
​	`alter table 副表 add foreign key(外键字段) references 主表(主键字段);`
## 获取外键约束名称
​	`show create table 表名;`
## 删除外键约束
​	`alter table 表名 drop foreign key 外键名;`
​	
## 把查询结果插入到指定表中，也就是表复制
​	`insert into .. select .. `
​    `insert into good_cates(name) select cate_name from goods group by cate_name;`
## 使用连接更新表中某个字段数据
​	`update goods g inner join good_cates gc on g.cate_name=gc.name set g.cate_name=gc.id;`
## 创建表并给某个字段添加数据
​	`create table good_brands (id int unsigned primary key auto_increment,name varchar(40) not null)select brand_name as name from goods group by brand_name;`
## 修改goods表结构
​	`alter table goods change cate_name cate_id int not null, change brand_name brand_id int not null;`