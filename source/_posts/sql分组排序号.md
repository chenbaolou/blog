---
title: sql分组排序号
date: 2017-02-07 17:16:42
tags:
---
需求描述如下图：
![Alt text](/images/2017-01-12-17-01-50屏幕截图.png)
前面两列已经确定，想生成第三列值，grn表示组内行号(group row number)。
找了网上很多例子，绝大数打乱原有的顺序。自己想了两种方案：
+ 方案1：
select id, goal, @grn:=CASE when @lastid=id then @grn+1 else 1 end as grn, @lastid:= id
from test1 a;
![Alt text](/images/2017-01-12-17-14-15屏幕截图-300x201.png)
第四列为了辅助计算，如果看着不爽，可以通过子查询再去过滤。

+ 方案2：
select @grn:=CASE when @id=id then @grn+1 else 1 end as grn, @id:=id as id, goal
from test1;
![Alt text](/images/2017-01-12-17-18-43屏幕截图.png)
缺点就是grn放到id之前了，因为id放后面可以担任方案1中的赋值行为。

其实这个需求是否合理也是值得商榷的。。。。。。。
