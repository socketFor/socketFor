---
title: "Mysql实例"
date: 2024-09-26
categories: 
  - "mysql"
tags: 
  - "mysql"
---

### 创建表

```
create table user(
id int primary key comment 'ID',
username varchar(100) not null comment '用户名',
password varchar(100) not null comment '密码',
time timestamp default current_timestamp comment '创建时间',
retime timestamp default current_timestamp comment '修改时间'

) comment '用户数据表';
```
