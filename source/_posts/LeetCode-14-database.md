---
title: LeetCode-14-database
date: 2020-03-29 20:45:14
tags: LeetCode
categories:
        - 数据结构与算法
        - LeetCode
---
## 十四、database

    优先顺序：where>group by>having>order by

### 182. 查找重复的电子邮箱
    链接：https://leetcode-cn.com/problems/duplicate-emails/

    SQL架构
    编写一个 SQL 查询，查找 Person 表中所有重复的电子邮箱。

    示例：

    +----+---------+
    | Id | Email   |
    +----+---------+
    | 1  | a@b.com |
    | 2  | c@d.com |
    | 3  | a@b.com |
    +----+---------+
    根据以上输入，你的查询应返回以下结果：

    +---------+
    | Email   |
    +---------+
    | a@b.com |
    +---------+
    说明：所有电子邮箱都是小写字母。

```
# Write your MySQL query statement below
# 1.使用group by 和临时表
select Email from
(
select Email, count(Email) as nums 
from Person
group by Email
) as tmp
where nums>1;
# 2.使用group by和having条件
select Email
from Person
group by Email
having count(Email) > 1;



```
