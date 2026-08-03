---
title: "Mysql"
date: 2024-07-13
categories: 
  - "mysql"
tags: 
  - "mysql"
coverImage: "8.jpg"
---

### Mysql安装：

官方安装：[https://dev.mysql.com/downloads/installer](https://dev.mysql.com/downloads/installer)

一路Next。

## Mysql的启动停止：

#### Mysql启动/停止

```
net start mysql80 //启动
net stop  mysql80 //停止
```

#### Mysql启动客户端

```
Mysql [-h 127.0.0.1] [-P 3306] -u root -p
Mysql -u root -p
```

## MySQL数据类型：

### 数值类型：

<figure>

| 类型 | 大小 | 范围（有符号） | 范围（无符号） | 用途 |
| --- | --- | --- | --- | --- |
| TINYINT | 1 Bytes |    (-128，127) | (0，255) | 小整数值 |
| SMALLINT | 2 Bytes |    (-32 768，32 767) | (0，65 535) | 大整数值 |
| MEDIUMINT | 3 Bytes |    (-8 388 608，8 388 607) |    (0，16 777 215) | 大整数值 |
| INT或INTEGER | 4 Bytes | (-2 147 483 648，2 147 483 647) | (0，4 294 967 295) | 大整数值 |
| BIGINT | 8 Bytes | (-9,223,372,036,854,775,808，9 223 372 036 854 775 807) | (0，18 446 744 073 709 551 615) | 极大整数值 |
| FLOAT | 4 Bytes | (-3.402 823 466 E+38，-1.175 494 351 E-38)，0，(1.175 494 351 E-38，3.402 823 466 351 E+38) | 0，(1.175 494 351 E-38，3.402 823 466 E+38) | 单精度   浮点数值 |
| DOUBLE | 8 Bytes | (-1.797 693 134 862 315 7 E+308，-2.225 073 858 507 201 4 E-308)，0，(2.225 073 858 507 201 4 E-308，1.797 693 134 862 315 7 E+308) | 0，(2.225 073 858 507 201 4 E-308，1.797 693 134 862 315 7 E+308) | 双精度   浮点数值 |
| DECIMAL | 对DECIMAL(M,D) ，如果M>D，为M+2否则为D+2 | 依赖于M和D的值 | 依赖于M和D的值 |    小数值 |

<figcaption>

\*数据来源于菜鸟教程

</figcaption>

</figure>

### 日期和时间类型：

<figure>

| 类型 | 大小   ( bytes) | 范围 | 格式 | 用途 |
| --- | --- | --- | --- | --- |
| DATE | 3 | 1000-01-01/9999-12-31 |    YYYY-MM-DD | 日期值 |
| TIME | 3 | '-838:59:59'/'838:59:59' |    HH:MM:SS | 时间值或持续时间 |
| YEAR | 1 | 1901/2155 | YYYY | 年份值 |
| DATETIME | 8 | '1000-01-01 00:00:00' 到 '9999-12-31 23:59:59' | YYYY-MM-DD hh:mm:ss | 混合日期和时间值 |
| TIMESTAMP | 4 | '1970-01-01 00:00:01' UTC 到 '2038-01-19 03:14:07' UTC   结束时间是第 **2147483647** 秒，北京时间 **2038-1-19 11:14:07**，格林尼治时间 2038年1月19日 凌晨 03:14:07 | YYYY-MM-DD hh:mm:ss | 混合日期和时间值，时间戳 |

<figcaption>

\*数据来源于菜鸟教程

</figcaption>

</figure>

### 字符串类型：

<figure>

| 类型 | 大小 | 用途 |
| --- | --- | --- |
| CHAR | 0-255 bytes | 定长字符串 |
| VARCHAR | 0-65535 bytes | 变长字符串 |
| TINYBLOB | 0-255 bytes | 不超过 255 个字符的二进制字符串 |
| TINYTEXT | 0-255 bytes | 短文本字符串 |
| BLOB | 0-65 535 bytes | 二进制形式的长文本数据 |
| TEXT | 0-65 535 bytes | 长文本数据 |
| MEDIUMBLOB | 0-16 777 215 bytes | 二进制形式的中等长度文本数据 |
| MEDIUMTEXT | 0-16 777 215 bytes | 中等长度文本数据 |
| LONGBLOB | 0-4 294 967 295 bytes | 二进制形式的极大文本数据 |
| LONGTEXT | 0-4 294 967 295 bytes | 极大文本数据 |

<figcaption>

\*数据来源于菜鸟教程

</figcaption>

</figure>

# SQL基础命令：

## DDL:

### DDL-数据库操作：

#### 查询：

查询所有数据库

```
show databases; //结尾一定要加; 和 空格！！
```

查询当前数据库

```
select databases(); 
```

#### 创建：

```
create database if not exists 数据名 [default charset utf8mb4\utf8]; 
```

#### 删除：

```
drop database if exists 数据名; 
truncate table 表名; 
```

#### 使用:

```
use 数据名; 
```

### DDL-表操作:

#### 创建：

```
create table 表名(
    字段1 字段1数据类型 [comment '注释'],
    字段2 字段2数据类型 [comment '注释'],
    字段3 字段3数据类型 [comment '注释'],
    字段4 字段4数据类型 [comment '注释']
) [comment '注释']; 
```

#### 时间默认值：

```
AUTO_INCREMENT //自动填写编号
primary key    //只能有一个值
TIMESTAMP DEFAULT CURRENT_TIMESTAMP
               //自动填写时间戳
DATE DEFAULT CURRENT_DATE   "仅适配8.0.13以上的mysql版本"
              //默认值设置当前时间
```

> #### 查询：

```
desc 表名; 
```

#### 添加/修改表内容：

```
alter table 表名 add 字段 字段数据类型 [comment '注释'];
                 modify    修改字段类型   (老，新)
                 change    修改字段       (老，新，新的字段类型)
                 drop      删除字段
                 rename to 修改表名       (老，新)
```

### DDL总结：

![](/wp-content/uploads/2024/07/1-1.png)

### 可视化Mysql：

破解版：[https://www.cnblogs.com/xiaohuhu/p/17218430.html](https://www.cnblogs.com/xiaohuhu/p/17218430.html)

![](/wp-content/uploads/2024/07/Snipaste_2024-07-14_17-46-38.png)

## DML

### DML-修改表内容操作：

#### 添加：

```
insert into 表单(栏1,栏2,栏3,栏4...) values(内容1,内容2,内容3,内容4...); 
```

#### 修改：

```
update 表单 set 修改栏 = '内容' where id = '位置'; 
```

#### 删除：

```
delete from 表单 where id = '位置'; 
```

### DML小结：

![](/wp-content/uploads/2024/07/Snipaste_2024-07-15_15-24-13.png)

## DQL

#### 虚拟数据实例

```
create table s_user(
    id int comment 'id',
    workid int comment '工作id',
    name varchar(50) comment '姓名',
    gender char(1) comment '性别',
    idcard varchar(50) comment '身份证',
    dress varchar(50) comment '地址',
    data date comment '日期'
);

INSERT INTO s_user (id, workid, name, gender, idcard, dress, data) VALUES
(1, 1001, '张三', 'M', '11010519800605371X', '北京市朝阳区XX街道XX号', '2023-04-01'),
(2, 1002, '李四', 'F', '32032220000101002X', '江苏省徐州市XX区XX路', '2023-04-02'),
(3, 1003, '王五', 'M', '510104199012121234', '四川省成都市XX区XX巷', '2023-04-03'),
(4, 1004, '赵六', 'F', '440306200503084567', '广东省深圳市XX区XX大厦', '2023-04-04'),
(5, 1005, '孙七', 'M', '220204197509200011', '吉林省吉林市XX县XX村', '2023-04-05'),
(6, 1006, '周八', 'F', '610103201008150002', '陕西省西安市XX区XX街', '2023-04-06'),
(7, 1007, '吴九', 'M', '340203198802150012', '安徽省芜湖市XX区XX路', '2023-04-07'),
(8, 1008, '郑十', 'F', '130102199507077890', '河北省石家庄市XX区XX小区', '2023-04-08'),
(9, 1009, '钱十一', 'M', '500236200204150001', '重庆市奉节县XX镇XX村', '2023-04-09'),
(10, 1010, '关十二', 'F', '150102201203040003', '内蒙古自治区呼和浩特市XX区XX街', '2023-04-10'),
(11, 1011, '冯十三', 'M', '210102197010010001', '辽宁省沈阳市XX区XX路', '2023-04-11'),
(12, 1012, '陈十四', 'F', '450103200806050004', '广西壮族自治区南宁市XX区XX街', '2023-04-12'),
(13, 1013, '卫十五', 'M', '360102198501010005', '江西省南昌市XX区XX路', '2023-04-13'),
(14, 1014, '蒋十六', 'F', '654002201502140006', '新疆维吾尔自治区塔城市XX区XX路', '2023-04-14'),
(15, 1015, '沈十七', 'M', '330106199207070007', '浙江省杭州市XX区XX路', '2023-04-15'),
(16, 1016, '韩十八', 'F', '420106200109100008', '湖北省武汉市XX区XX街', '2023-04-16'),
(17, 1017, '杨十九', 'M', '140222198311050009', '山西省大同市XX县XX乡', '2023-04-17'),
(18, 1018, '秦二十', 'F', '530102200703080010', '云南省昆明市XX区XX路', '2023-04-18'),
(19, 1019, '何廿一', 'M', '110108199702145678', '北京市海淀区XX街道XX号', '2023-04-19'),
(20, 1020, '郭廿二', 'F', '370202200301010011', '山东省青岛市XX区XX路', '2023-04-20');
```

#### DQL基本查询命令：

```
--查看局部
select [name, gender, idcard] from s_user; //[]中为筛选名
--查看全部
select * from s_user;
--创建别名
select name '姓名' from s_user; //别名可以更好方便查看
--查看内容(去除重复)
select distinct gender '性别' from s_user;
```

#### 条件查询：

```
--查看条件查询内容
select 所以表单名 from s_user where 查询条件; 
------------------------------------------------------------------
-- 年龄等于30
select * from employee where age = 30;
-- 年龄小于30
select * from employee where age < 30;
-- 小于等于
select * from employee where age <= 30;
-- 没有身份证
select * from employee where idcard is null or idcard = '';
-- 有身份证
select * from employee where idcard;
select * from employee where idcard is not null;
-- 不等于
select * from employee where age != 30;
-- 年龄在20到30之间
select * from employee where age between 20 and 30;
select * from employee where age >= 20 and age <= 30;
-- 下面语句不报错，但查不到任何信息
select * from employee where age between 30 and 20;
-- 性别为女且年龄小于30
select * from employee where age < 30 and gender = '女';
-- 年龄等于25或30或35
select * from employee where age = 25 or age = 30 or age = 35;
select * from employee where age in (25, 30, 35);
-- 姓名为两个字
select * from employee where name like '__';
-- 身份证最后为X
select * from employee where idcard like '%X';
```

##### 查询条件

![](/wp-content/uploads/2024/07/Snipaste_2024-07-15_16-47-51-1024x324.png)

#### 总和查询（聚合函数）：

```
select 聚合函数 from s_user;
-----------------------------------------
select count(name) from s_user;//查询所有人数
```

![](/wp-content/uploads/2024/07/Snipaste_2024-07-15_17-01-14.png)

#### 分组条件查询：

```
select 表单名 from [where 条件查询] group by 表单名 [having 条件];
----------------------------------------------------------------
--查询执行优先级 
where > 聚合函数 > having 
----------------------------------------------------------------
-- 根据性别分组，统计男性和女性数量（只显示分组数量，不显示哪个是男哪个是女）
select count(*) from employee group by gender;
-- 根据性别分组，统计男性和女性数量
select gender, count(*) from employee group by gender;
-- 根据性别分组，统计男性和女性的平均年龄
select gender, avg(age) from employee group by gender;
-- 年龄小于45，并根据工作地址分组
select workaddress, count(*) from employee where age < 45 group by workaddress;
-- 年龄小于45，并根据工作地址分组，获取员工数量大于等于3的工作地址
select workaddress, count(*) address_count from employee where age < 45 group by workaddress having address_count >= 3;
```

#### 排序查询：

```
select * from s_user order by data desc; 
-------------------------------------------------------------------
升序: asc
倒序: desc
-------------------------------------------------------------------
-- 根据年龄升序排序
SELECT * FROM employee ORDER BY age ASC;
SELECT * FROM employee ORDER BY age;
-- 两字段排序，根据年龄升序排序，入职时间降序排序
SELECT * FROM employee ORDER BY age ASC, entrydate DESC;
//如果是多字段排序，当第一个字段值相同时，才会根据第二个字段进行排序
```

#### 分页查询：

```
select 字段列表 from 表名 limit 起始索引, 查询记录数;
------------------------------------------------------
-- 查询第一页数据，展示10条
SELECT * FROM employee LIMIT 0, 10;
-- 查询第二页
SELECT * FROM employee LIMIT 10, 10;
------------------------------------------------------
FROM -> WHERE -> GROUP BY -> SELECT -> ORDER BY -> LIMIT
```

#### DQL小结：

![](/wp-content/uploads/2024/07/Snipaste_2024-07-15_18-11-10.png)
