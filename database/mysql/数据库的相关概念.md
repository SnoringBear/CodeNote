### 数据库的相关概念

### 一、数据库的好处

1、可以持久化数据到本地
2、结构化查询

### 二、数据库的常见概念

1、DB：数据库，存储数据的容器
2、DBMS：数据库管理系统，又称为数据库软件或数据库产品，用于创建或管理DB
3、`SQL`：结构化查询语言，用于和数据库通信的语言，不是某个数据库软件特有的，而是几乎所有的主流数据库软件通用的语言

### 三、数据库存储数据的特点

1、数据存放到表中，然后表再放到库中
2、一个库中可以有多张表，每张表具有唯一的表名用来标识自己
3、表中有一个或多个列，列又称为“字段”，相当于`java`中“属性”
4、表中的每一行数据，相当于`java`中“对象”

### 四、常见的数据库管理系统

`mysql`、oracle、`db2`、`sqlserver`

### `MySQL`的介绍

### 一、`MySQL`的背景

前身属于瑞典的一家公司，`MySQL` AB
08年被sun公司收购
09年sun被oracle收购

### 二、`MySQL`的优点

1、开源、免费、成本低
2、性能高、移植性也好
3、体积小，便于安装

### 三、`MySQL`的安装

属于c/s架构的软件，一般来讲安装服务端
企业版
社区版

5.5
5.6
5.7
8.0

### 四、`MySQL`服务的启动和停止

方式一：通过命令行
net start 服务名
net stop 服务名
方式二：计算机——右击——管理——服务

## DQL语言

### 基础查询

### 一、语法

select 查询列表
from 表名;

### 二、特点

1、查询列表可以是字段、常量、表达式、函数，也可以是多个
2、查询结果是一个虚拟表

### 三、示例

1、查询单个字段
select 字段名 from 表名;

2、查询多个字段
select 字段名，字段名 from 表名;

3、查询所有字段
select * from 表名

4、查询常量
select 常量值;
注意：字符型和日期型的常量值必须用单引号引起来，数值型不需要

5、查询函数
select 函数名(实参列表);

6、查询表达式
select 100/1234;

7、起别名
①as
②空格

8、去重
select distinct 字段名 from 表名;

9、+
作用：做加法运算
select 数值+数值; 直接运算
select 字符+数值;先试图将字符转换成数值，如果转换成功，则继续运算；否则转换成0，再做运算
select null+值;结果都为null

10、【补充】concat函数
功能：拼接字符
select concat(字符1，字符2，字符3,...);

11、【补充】ifnull函数
功能：判断某字段或表达式是否为null，如果为null 返回指定的值，否则返回原本的值
select ifnull(commission_pct,0) from employees;

12、【补充】isnull函数
功能：判断某字段或表达式是否为null，如果是，则返回1，否则返回0

### 条件查询

### 一、语法

select 查询列表
from 表名
where 筛选条件

### 二、筛选条件的分类

1、简单条件运算符

```
	< = <> != >= <=  <=>安全等于
```

2、逻辑运算符
&& and
|| or
!  not

3、模糊查询
like:一般搭配通配符使用，可以判断字符型或数值型
通配符：%任意多个字符，_任意单个字符

```
	between and
	in
	is null /is not null：用于判断null值

	is null PK <=>
				普通类型的数值	null值		可读性
				is null		×			√		√
				<=>		√			√		×
```

### 排序查询

### 一、语法

select 查询列表
from 表
where 筛选条件
order by 排序列表 【`asc`、`desc`】

### 二、特点

1、`asc` ：升序，如果不写默认升序
`desc`：降序

2、排序列表 支持 单个字段、多个字段、函数、表达式、别名

3、order by的位置一般放在查询语句的最后（除limit语句之外）

### 常见函数

### 一、概述

功能：类似于`java`中的方法
好处：提高重用性和隐藏实现细节
调用：select 函数名(实参列表);

### 二、单行函数

1、字符函数
`concat`:连接
`substr`:截取子串       // index 从1开始
upper:变大写
lower：变小写
replace：替换
length：获取字节长度
trim:去前后空格
`lpad`：左填充
`rpad`：右填充
instr:获取子串第一次出现的索引
2、数学函数
`ceil`:向上取整
round：四舍五入
mod:取模
floor：向下取整
truncate:截断
rand:获取随机数，返回0-1之间的小数

3、日期函数

```
	now：返回当前日期+时间
	year:返回年
	month：返回月
	day:返回日
	date_format:将日期转换成字符
	curdate:返回当前日期
	str_to_date:将字符转换成日期
	curtime：返回当前时间
	hour:小时
	minute:分钟
	second：秒
	datediff:返回两个日期相差的天数
	monthname:以英文形式返回月
```

4、其他函数
version 当前数据库服务器的版本
database 当前打开的数据库
user当前用户
password('字符')：返回该字符的密码形式
md5('字符'):返回该字符的md5加密形式

5、流程控制函数

```
	①if(条件表达式，表达式1，表达式2)：如果条件表达式成立，返回表达式1，否则返回表达式2
	②case情况1
			case 变量或表达式或字段
			when 常量1 then 值1
			when 常量2 then 值2
			...
			else 值n
			end

	③case情况2
			case
			when 条件1 then 值1
			when 条件2 then 值2
			...
			else 值n
			end
```

### 三、分组函数

1、分类
max 最大值
min 最小值
sum 和
avg 平均值
count 计算个数

2、特点

```
	①语法
			select max(字段) from 表名;

	②支持的类型
			sum和avg一般用于处理数值型
			max、min、count可以处理任何数据类型

	③以上分组函数都忽略null
	④都可以搭配distinct使用，实现去重的统计
			select sum(distinct 字段) from 表;
	⑤count函数
			count(字段)：统计该字段非空值的个数
			count(*):统计结果集的行数
			案例：查询每个部门的员工个数
			1 xx    10
			2 dd    20
			3 mm    20
			4 aa    40
			5 hh    40

	count(1):统计结果集的行数

	效率上：
			MyISAM存储引擎，count(*)最高
			InnoDB存储引擎，count(*)和count(1)效率>count(字段)

	⑥ 和分组函数一同查询的字段，要求是group by后出现的字段
```
