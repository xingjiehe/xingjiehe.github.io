---

title: sql语句
date: 2022-11-01 15:47:34
tags:
    - 数据库
    - 工作随记
categories: SQL学习
---
# SQL语句

###### 1. 查询多人重复的id 注意用GROUP BY

```sql
SELECT dc_ou FROM calculate_person_list_confirm WHERE salary_group = '173_03' AND salary_calculate_region = '202209' GROUP BY dc_ou;
```

<!-- more -->

###### 2. 查询多个条件 可用in 

​	⚠️ oracle里in语句不能超过1000条，否则容易出问题。
[解决超过1000条](https://blog.csdn.net/weixin_42825651/article/details/123045574)

```sql
SELECT calculate_type FROM salary_file_detail WHERE id in (1,2,3);
```

