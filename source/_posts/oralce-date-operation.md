---
reward: false
title: oralce数据库时间操作
date: 2018-02-06 14:15:00
categories: software
tags:
     - date
     - oracle
---
<!-- more -->
#### 查看数据库版本
- ##### v$version
SQL> select * from v$version;<br/>
![](/images/20180129093548.png)

- ##### product_component_version
SQL> select * from product_component_version;<br/>
![](/images/20180129094432.png)

#### 获取年月日时分秒
- ##### 获取年
select to_char(sysdate,'yyyy') from dual; -- 2018
select to_char(sysdate,'YYYY') from dual; -- 2018

- ##### 获取月
select to_char(sysdate,'mm') from dual; -- 1
select to_char(sysdate,'MM') from dual; -- 1

- ##### 获取日
select to_char(sysdate,'dd') from dual; -- 29
select to_char(sysdate,'DD') from dual; -- 29

- ##### 获取时
select to_char(sysdate,'hh') from dual; -- 04
select to_char(sysdate,'HH') from dual; -- 04
select to_char(sysdate,'hh24') from dual; -- 16
select to_char(sysdate,'HH24') from dual; -- 16

- ##### 获取分钟
select to_char(sysdate,'mi') from dual; -- 03
select to_char(sysdate,'MI') from dual; -- 03

- ##### 获取秒
select to_char(sysdate,'ss') from dual; -- 40
select to_char(sysdate,'SS') from dual; -- 40

- ##### 获取年月日时分秒
select to_char(sysdate,'yyyy-mm-dd hh:mi:ss') from dual; -- 2018-01-29 16:06:15
select to_char(sysdate,'yyyy-mm-dd hh24:mi:ss') from dual; -- 2018-01-29 16:06:15

 *说明:*
(1) 从上面的测试SQL可以看出yyyy, mm, dd, hh, mi, ss的大小写对于从oracle中获取年月日时分秒没有影响
(2) 对于获取小时分12小时和24小时的情况，如果你想显示下午的时间，你可以采用hh24, 这样就会显示类似于15:30而不是03:30

#### 获取一周前，一个月前，一年前的日期
- ##### 获取当前时间一周前的时间
SQL> select sysdate - interval '7' day from dual;
![](/images/20180129101923.png)

  类似的(其他)
```
-- 当前时间减去7分钟的时间
select sysdate, sysdate - interval '7' minute from dual;
-- 当前时间减去7小时的时间
select sysdate, sysdate - interval '7' hour from dual;
-- 当前时间减去7天的时间
select sysdate, sysdate - interval '7' day from dual;
-- 当前时间减去7月的时间
select sysdate, sysdate - interval '7' month from dual;
-- 当前时间减去7年的时间
select sysdate, sysdate - interval '7' year from dual;
-- 时间间隔乘以一个数字(16小时)
select sysdate, sysdate - 8 *interval '2' hour from dual;
```
  ![](/images/20180129102848.png)

#### 根据当前时间获取最近5年，最近6个月，最近30天的时间
- ##### 根据当前时间获取最近5年时间：(yyyy)
```
select level as "level",to_char(sysdate,'yyyy') - level + 1 as "xAxisValue" from dual
connect by level <= 5
order by to_char(sysdate,'yyyy') - level + 1 asc
```
 ![](/images/20180129103913.png)

- ##### 根据当前时间获取最近6个月的时间：(yyyy-mm)

1. 先拆解一下SQL语句
``` bash
select trunc(sysdate, 'mm')+1 from dual;-- 2018/1/2
select to_char(trunc(sysdate, 'mm')+1, 'yyyy-mm') from dual;-- 2018-01
select to_date(to_char(trunc(sysdate, 'mm')+1, 'yyyy-mm'),'yyyy-mm') from dual;-- 2018/1/1
select add_months(to_date(to_char(trunc(sysdate, 'mm')+1, 'yyyy-mm'),'yyyy-mm'),-1) from dual;-- 2017/12/1
select to_char(add_months(to_date(to_char(trunc(sysdate, 'mm')+1, 'yyyy-mm'),'yyyy-mm'),-1),'yyyy-mm') from dual;-- 2017-12
```

2. 执行SQL语句:
```
select to_char(add_months(to_date(to_char(trunc(sysdate, 'mm')+1, 'yyyy-mm'),'yyyy-mm'),-(rownum - 1)),'yyyy-mm') as xAxisValue from dual
connect by rownum <=6
order by xAxisValue asc
```

3. 结果:
![](/images/20180129111527.png)

- ##### 根据当前时间获取最近30天时间：(yyyy-mm-dd)
```
select to_char(day,'yyyy-mm-dd') as "xAxisValue" from
(select to_date(to_char(trunc(sysdate,'dd'),'yyyy-mm-dd'),'yyyy-mm-dd') - rownnum + 1 as day from dual
connect by rownum <= 30) order by day asc
```
  也可以根据输入启止时间来获取时间段内的天：如下为获取2017-06-10到2017-07-10时间段内的天(yyyy-mm-dd)
```
select to_char(day,'yyyy-mm-dd') from
(select to_date(to_char(trunc(to_date('2017-07-10','yyyy-mm-dd'),'dd'),'yyyy-mm-dd'),'yyyy-mm-dd') - rownum + 1 as day from dual
connect by rownum <= to_date('2017-07-10','yyyy-mm-dd') - to_date('2017-06-10','yyyy-mm-dd') + 1) order by day asc
```

#### 参考：
如何查看Oracle数据库版本: [https://www.2cto.com/database/201209/155673.html](https://www.2cto.com/database/201209/155673.html)
oracle中获取年月日时分秒: [http://blog.csdn.net/u012934325/article/details/52778136](http://blog.csdn.net/u012934325/article/details/52778136)
Oracle获取一周前，一个月前，一年前的日期:
[http://blog.csdn.net/siqilou/article/details/40887919](http://blog.csdn.net/siqilou/article/details/40887919)
Oracle根据当前时间获取最近5年，最近6个月，最近30天的时间：[http://blog.csdn.net/maple_fix/article/details/74926226](http://blog.csdn.net/maple_fix/article/details/74926226)





