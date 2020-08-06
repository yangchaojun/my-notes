# SQL

1. 什么是SQL？

   Structured Query Language：结构化查询语言

   其实就是定义了操作所有关系型数据库的规则。每一种数据库操作的方式存在不一样的地方，称为“方言”。

2. SQL通用语法

   1. SQL语句可以单行或多行书写，以分号结尾。
   2. 可使用空格和缩进来增进语句可读性。
   3. MySQL数据的SQL语句不区分大小写，关键字建议使用大写。
   4. 3种注释
      - 单行注释：-- 注释内容 或 # 注释内容
      - 多行注释 /* 注释 */

3. SQL分类

   1. DDL（操作数据库、表）
   2. DML（增删改表中数据）
   3. DQL （Data Query Language查询表中的数据）
   4. DCL （授权）
   5. TCL （事务控制）

## DDL：操作数据库、表

1. 操作数据库：CRUD

   1. C(Create)：创建
   2. R (Retrieve)：查询
   3. U (Update)：修改
   4. D（Delete)：删除
   5. 使用数据库

2. 操作表：CRUD

   数据库类型：

    	1. int： 整型类型
        - age int,
    	2. double：小数类型
        - score: double(5, 2)
    	3. date：日期，只包含年月日，yyyy-MM-dd
    	4. datetime：日期，包含年月日时分秒，yyyy-MM-dd HH:mm:ss
    	5. timestamp：时间戳类型，包含年月日时分秒， yyyy-MM-dd HH:mm:ss
        - 如果将来不给这个字段赋值，或赋值为null，则默认使用当前系统时间来自动赋值
   	6. varchar：字符串
       - name varchar(20)：姓名最大20个字符

## MySQL常见命令

```sql
-- 展示所有数据库
show databases;
-- 使用数据库
use db1;
-- 展示所有表
show tables;
-- 展示表结构
desc table stu;
-- 查看数据库当前版本
select version();
```

## MySQL的语法规范

1. 不区分大小写，但建议关键字大写，表名、列名小写
2. 每条命令最好用分号结尾
3. 每条命令根据需要，可以进行缩进 或 换行
4. 注释
   1. 单行注释：`-- 注释内容`
   2. 单行注释：`#注释内容`
   3. 多行注释：`/* 注释文字 */`

## MySQL练习

1. 基础查询
   1. 语法 `select 查询列表 from 表名`
   2. 特点
      1. 查询列表可以是：表中的字段、常量值、表达式、函数
      2. 查询的结果是一个虚拟的表格
   3. 练习
      1. 查询表中的单个字段 `SELECT last_name FROM employees;`
      2. 查询表中的多个字段`SELECT last_name,salary,email FROM employees;`
      3. 查询表中的所有字段`SELECT * FROM employees;`
      4. 查询常量值`SELECT 100;`
      5. 查询表达式`SELECT 100 % 98;`
      6. 查询函数`SELECT VERSION();`
      7. 起别名`SELECT 100 % 98 AS 结果;` `SELECT last_name 姓, first_name 名 FROM employees;`
      8. 去重`SELECT DISTINCT department_id FROM employees;`
      9. +号的作用
   
2. 条件查询
   1. 语法：`select 查询列表 from 表名 where 筛选条件;`
   2. 分类：
      1. 按条件表达式筛选
         1. 条件运算符： `> < = != <> >= <=`
      2. 按逻辑表达式筛选
         1. 逻辑运算符：` and or not`
      3. 模糊查询
         1. `like`
            1. 通配符：
               1. % 任意多个字符，包含0个字符
               2. _ 任意单个字符
               3. `ESCAPE`转义
         2. `between and` 等价于`>=  and <=`
         3. `in`
         4. `is null`  `is not null` 安全等于 `<=>`
   
3. 排序查询
   1. 语法 `select 查询列表 from 表 where 筛选条件 order by [asc | desc]`
   2. 特点：
      1. asc 代表升序，desc 代表降序，默认不写为asc升序
      2. order by子句中可以支持单个字段、多个字段、表达式、函数、别名
      3. order by字句一般是放在查询语句的最后面，limit字句除外
   
4. 常见函数

   1. 分类：单行函数、分组函数（统计函数、聚合函数、组函数）

      - 字符函数 length、concat、upper、lower、substr、instr、trim、lpad、rpad(填充)、replace

      - 数学函数 

        - round 四舍五入
        - ceil 向上取整，返回大于等于该参数的最小整数
        - floor 向下取整，返回小于等于该参数的最大整数
        - truncate 截断
        - mod 取余

      - 日期函数

        - now 返回当前系统日期+时间
        - curdate 返回当前系统时间，不包含时间
        - curtime 返回当前时间，不包含日期
        - year 返回年份
        - month 返回月份
        - 。。。
        - str_to_date 将日期格式的字符转换成指定格式的日期
        - date_format 将日期转换成字符

      - 其他函数

        - version()
        - database()
        - user()

      - 流程控制函数

        - if if ... else的效果

        - case 函数

          - switch case 的效果 

          - ```sql
            case 要判断的字段或表达式
            when 常量1 then 要显示的值1 或 语句1
            when 常量2 then 要显示的值2 或 语句2
            ...
            else 要显示的值n 或 语句n
            end
            ```

          - 类似多重if

          - ```sql
            case
            when 条件1 then 要显示的值1或语句1
            when 条件2 then 要显示的值2或语句2
            ...
            else 要显示的值n或语句n
            end
            ```

            

