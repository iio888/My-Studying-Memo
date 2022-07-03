# MySQL



 ## 安装

### 步骤

1. 下载安装包

   地址：[<dev.mysql.com/downloads/windows/installer/8.0.html>]()

2. 配置环境变量

3. 菜单汉化

   地址：<https://pan.baidu.com/s/1uhqp_hAqB4zRpwd66obgUQ>

   提取码：1c5i

### 问题

- **start server 失败**

  1. 打开 服务

     运行 -> services.msc

  2. 找到 MySQL

  3. 打开 属性 -> 登录

  4. 登录身份设为 本地系统账户

- **最后验证时密码错误**

  1. 用管理员权限打开cmd，cd 至安装目录下

     `C:/Program files/MySQL/MySQL Server 8.0/bin`

  2. 输入：`mysql -u root -p`
  
  3. 尝试输入密码：安装时设置的 or 直接回车（无密码）
  
  4. 停止 mysql 服务
  
     `net stop mysql`
  
  5. 初始化并查看日志
  
     `mysqld --initialize --console`
  
  6. localhost 之后就是随机生成的密码
  
  7. 启动 mysql 服务
  
  8. 按照步骤 `2`，输入新生成的密码
  
  9. 修改密码
  
     `ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '新密码';`
  
  10. `quit` 离开





## SQL



### 语法



#### 关键字

##### 注意：

1. 关键字顺序

   `SELECT` - `FROM` - `WHERE` - `ORDER BY` - `LIMIT`

2. 逻辑运算符优先级

   AND > OR

##### USE

选择数据库

`USE sql_database;`

##### DISTINCT

删除结果中的重复项

`SELECT DISTINCT column_name FROM table1`

##### AND OR NOT

作为 WHERE 的并列条件

`WHERE order_id = 6 AND unit_price*quantity > 30`

##### IN

`WHERE state IN ('VA','FL','GA')`

同等于 `WHERE state = 'VA' OR  state = 'FL' OR state = 'GA' `

##### BETWEEN

`WHERE points BETWEEN 1000 AND 3000 `

##### LIKE

`WHERE last_name LIKE 'b%' `

##### REGEXP

`WHERE last_name REGEXP 'field' `

同等于 `WHERE last_name LIKE '%field%' `

##### IS NULL

检索空值

`WHERE phone IS NULL`



#### 正则表达式

关键字：REGEXP

无符号表示包含该字符串

| 符号 |             含义              |        例句        |
| :--: | :---------------------------: | :----------------: |
|  ^   |         以 filed 开头         |      '^filed'      |
|  $   |         以 field 结尾         |      'filed$'      |
|  \|  | 包含 field 或者 mac 或者 rose | 'field\|mac\|rose' |
|  []  |      包含 gi 或 ie 或 me      |      '[gim]e'      |
|  -   |    表示 a-h 之间的所有字符    |      '[a-h]e'      |



#### SELECT

1. 使用 AS 给列和结果集起别名

   `SELECT points*10+100 AS discount_factor FROM customers`

2. 别名中使用空格

   在别名前后加上单引号或者双引号

   `SELECT points AS 'discount factor' FROM customers`



#### ORDER BY

不使用 `ORDER BY` 默认按照主键排序。*（MySQL特性）*可以排序任意列数据，不论该列是否在 SELECT 的范围中。

- **降序 DESC**

  `ORDER BY points DESC`

- **升序 ASC**

  `ORDER BY points ASC`

- **排序次序**

  `ORDER BY state DESC, points ASC`  

  先按照 state 排序，再按照 points 排序



#### LIMIT

- **限制显示条数**

  `LIMIT 3`		只显示前三条记录

- **偏移量**

  `LIMIT 6，3`	跳过前面6条记录，显示三条记录



#### 通配符

| 字符 |        含义        |
| :--: | :----------------: |
|  %   | 表示任意个任意字符 |
|  _   |    表示单个字符    |



#### Tips

1. **<>：**不等于，相当于 !=

2. SQL 不区分大小写

3. 字符串前后要带上双引号或者单引号

4. 日期标准格式：

   `'1990-01-01'`

5. `--(space)  `：注释

6. 用  `;` 断句

5. 表格别名：`FROM orders o`    `JOIN orders o`  





### 连接方式

连接表格

#### 内连接

关键字：`(INNER) JOIN`

示例：`JOIN customers ON orders.customer_id = customers.customer_id`

含义：基于两张表的 customer_id 连接

- 连接多表

  写两个 JOIN



#### 自连接

和同一张表连接



#### 复合连接

使用多个条件连接表格

```sql
SELECT *
FROM order_items oi
JOIN order_item_notes oin
	ON oi.order_id = oin.order_id
	AND oi.product_id = oin.product_id
```



#### 隐式连接

```sql
SELECT *
FROM orders o
JOIN customers c, orders o
WHERE o.customer_id = c.customer_id
```

