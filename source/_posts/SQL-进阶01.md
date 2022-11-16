---
title: SQL-进阶01
date: 2022-11-10 11:31:48
tags:
  - 数据库
category:
  - SQL学习
---

## SELECT语句实例

<!-- more -->

[b站视频](https://www.bilibili.com/video/BV1UE41147KC/?p=8&spm_id_from=pageDriver&vd_source=e70da3479cf8b9e3771c773a404a3780)

```sql
USE sql_store
SELECT 
	DISTINCT last_name, 
	first_name, 
	points,
	-- 运算符逻辑与数学一致
	(points + 70) % 100 AS discount_fact 或者 'discount factor'
FROM customers
```

<!-- more -->

- **练习**

Return all the products. 展示name, unit price 和 new price (unit price*1.1)

```sql
SELECT 
	name, 
	unit_price, 
  unit_price * 1.1 AS 'new price' 
  -- 或者 unit_price * 1.1 AS 'new price'
FROM products;
```

***

## WHERE语句实例

```sql
USE sql_store;
SELECT * FROM customers WHERE state <> 'va'或者'VA'
```

也可对日期或文本进行比较运算，注意SQL里日期的标准写法及其需要用引号包裹这一点

```sql
WHERE birth_date > '2022-01-01'
```

- **练习**

Get the orders placed this year(2019)

```sql
SELECT * FROM orders WHERE order_date >= '2019-01-01';	
```

***

## AND, OR, NOT运算实例

- **小结**

  用逻辑运算符AND、OR、NOT对（数学和）比较运算进行组合实现多重条件筛选
  执行优先级：数学→比较→逻辑

```sql
SELECT * FROM customers 
WHERE birth_date > '1990-01-01' AND points > 1000
/ WHERE birth_date > '1990-01-01' OR 
      points > 1000 AND state = 'VA'
```

1. **AND优先级高于OR，但最好加括号，更清晰**

```sql
WHERE birth_date > '1990-01-01' OR 
      (points > 1000 AND state = 'VA')
```

2. **NOT的用法**

```sql
SELECT * FROM customers 
WHERE NOT (birth_date > '1990-01-01' OR points > 1000)
-- 去掉NOT 相当于
WHERE birth_date <= '1990-01-01' AND points <= 1000
```

- 练习

订单6中总价大于30的商品

```sql
SELECT * FROM order_items where order_id = 6 
AND quantity * unit_price > 30;
```

***

## IN运算符实例

可加NOT，进行取反

**练习**

Return products with quantity in stock equal to 49, 38, 72

```sql
SELECT * FROM products where quantity_in_stock in (49,38,72)
```

***

## BETWEEN运算符实例

⚠️注意事项：

1. 用AND而非括号

2. **闭区间，包含两端点**

3. 注意两端都是包含的 不能写作BETWEEN (1000, 3000)！别和IN的写法搞混

**练习**

Return customers born between 1/1/1990 and 1/1/2000

```sql
SELECT * FROM customers WHERE birth_date BETWEEN '1990-01-01' AND '2000-01-01'
```

***

## LIKE运算符实例

**小结**

模糊查找，查找具有某种模式的字符串的记录/行

```sql
SELECT * FROM customers
WHERE last_name LIKE 'brush%' / 'b____y'/'%b%'
-- 'b____y'这个表示匹配第一个字符是b然后紧跟4个字符，最后一个为y
```

两种通配符：

- `%` 任何个数（包括0个）的字符
- `_` 单个字符

**练习**

Get the customers whose addresses contain TRAIL or AVENUE, phone numbers end with 9

```sql
SELECT * FROM customers WHERE address LIKE '%TRAIL%' or address LIKE '%AVENUE%'
SELECT * FROM customers WHERE phone LIKE '%9'
```

***

## REGEXP运算符

**小结**

正则表达式，在搜索字符串方面更为强大，可搜索更复杂的模板

**例子**

```sql
SELECT * FROM customers
WHERE last_name LIKE '%field%'
等效于：
WHERE last_name REGEXP 'field'
```

正则表达式可以组合来表达更复杂的字符串模式

```sql
WHERE last_name REGEXP '^mac|field$|rose' -- 查找mac开头或field结尾或包含rose的结果
WHERE last_name REGEXP '[gi]e|e[fmq]' -- 查找含ge/ie或ef/em/eq的
WHERE last_name REGEXP '[a-h]e|e[c-j]'-- 查找e之前a到h任意字母的结果
```

| 符号  | 意义           |
| ----- | -------------- |
| ^     | 开头           |
| $     | 结尾           |
| [abc] | 含abc          |
| [a-c] | 含a到c         |
| \|    | logical or或者 |

**练习**

Get the customers whose 1.first names are ELKA or AMBUR 2.last name end with EY or ON

3.last names start with MY or contains CE 4.last name contain B followed by R or U

```sql
SELECT * FROM customers WHERE first_name REGEXP 'ELKA|AMBUR';
SELECT * FROM customers WHERE last_name REGEXP 'EY$|ON$';
SELECT * FROM customers WHERE last_name REGEXP '^MY|SE';
SELECT * FROM customers WHERE last_name REGEXP 'b[RU]'或者'br|bu';
```

***

## IS NULL运算符

**小结**

找出空值，找出有某些属性缺失的记录

**例子**

找出phone缺失的数据

```sql
SELECT * FROM customers WHERE phone IS NULL / IS NOT NULL
```

**练习**

Get the orders that are not shipped

```sql
SELECT * FROM orders WHERE shipper_id IS NULL;
```

***

## ORDER BY语句

**小结**

1. 可多列
2. **可以是列间的数学表达式**
3. **可包括任何列，包括没选择的列（MySQL特性，其它DBMS可能报错），**
4. **可以是之前定义好的别名列（MySQL特性，甚至可以是用一个常数设置的列别名）**
5. 任何一个排序依据列后面都可选加 DESC

**例子**

```sql
SELECT name, unit_price * 1.1 + 10 as new_price 
from products
order by new_price desc, product_id
-- 这两个分别是 别名列 和 未选择列，都用到了 MySQL 特性
```

**练习**

订单2的商品按总价降序排列

```sql
SELECT * FROM order_items WHERE order_id = '2' 
ORDER BY quantity * unit_price DESC;
-- 或者先定义别名
SELECT *, quantity * unit_price AS price
FROM order_items WHERE order_id = '2'
ORDER BY price DESC;
```

***

## LIMIT子句

**小结**

限制返回结果的记录数量，“前N个” 或 “跳过M个后的前N个”。<u>**LIMIT子句一定要放在最后**</u>

**例子**

网页分页，page1: 1-3客户 page2: 4-6客户 page3: 7-9客户，取page3的客户

```sql
-- 跳过前六条记录，取后面三条
SELECT * FROM custmoers LIMIT 6, 3
-- 6是偏移量
```

**练习**

Get the top three loyal customers 

```sql
SELECT * FROM customers ORDER BY points DESC LIMIT 3;
```

***

## TO SUM UP

**子句顺序**

```sql
select`+ `from` + `where` + `order by` + `limit
```



