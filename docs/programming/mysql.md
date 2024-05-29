# MySQL

## 准备工作

### 安装

```shell
# 安装
sudo apt install mysql-server
# 显示安装包信息
apt-cache show mysql-server
# 查看MySQL服务效果图(ps查看所有进程，-a显示所有用户，-u不显示用户名，-x不显示所有的执行程序)
ps -aux | grep mysql
```

### MySQL 服务

```shell
# 查看MySQL的服务状态
sudo service mysql status
# 停止MySQL服务
sudo service mysql stop
# 启动MySQL服务
sudo service mysql start
# 重启MySQL服务
sudo service mysql restart
```

### 配置文件

```shell
❯ cd /etc/mysql/mysql.conf.d
❯ ls
mysql.cnf  mysqld.cnf
```

- `port` 表示端口号，默认为 3306；
- `bind-address` 表示服务器绑定的 IP，默认为 127.0.0.1；
- `datadir` 表示服务器的保存路径，默认为 `/var/lib/mysql`
- `log_error` 表示错误日志，默认为 `/var/log/mysql/error.log`

## 数据类型和约束

### 数据类型

在创建表的时候为表中字段指定数据类型，只有数据符合类型要求才能存储起来，使用数据类型的原则是——够用就行，尽量使用取值范围小的，而不用大的，这样可以更多的节省存储空间。

- 整数：int, bit
- 小数：decimal，例如 `decimal(5,2)` 表示存 5 位数，小数占 2 位
- 字符串：varchar, char，`char` 表示固定长度的字符串 `char(3)` 存储 `'ab'` 时会补一个空格存为 `'ab '`；`varchar` 时可变长度的字符串 `varchar(3)` 存储 `'ab'` 时会存为 `'ab'`
- 大文本：text
- 日期时间：data, time, datetime
- 枚举类型：enum

> 对于图片、音频、视频等文件，不存在数据库中，而是上传到某个服务器，然后表中存储这个文件的保存路径

### 数据约束

- 主键 `primary key`：物理上存储的顺序，MySQL 建议所有表的主键字段都叫 id，类型为 int unsigned
- 非空 `not null`：此字段不允许填写空格
- 唯一 `unique`：此字段不允许重复
- 默认 `default`：此字段不填写时使用默认值
- 外键 `foreign key`：约束关系字段，当关系字段填写值时，会关联到表中查询此值是否存在，如果存在则填写成功，如果不存在则填写失败并抛出异常

### 附表

- 整数类型

|    类型     | 字节大小 |             有符号范围(signed)             |   无符号范围(unsigned)   |
| :---------: | :------: | :----------------------------------------: | :----------------------: |
|   TINYINT   |    1     |                 -128 ~ 127                 |         0 ~ 255          |
|  SMALLINT   |    2     |               -32768 ~ 32767               |        0 ~ 65535         |
|  MEDIUMINT  |    3     |             -8388608 ~ 8388607             |       0 ~ 16777215       |
| INT/INTEGER |    4     |          -2147483648 ~2147483647           |      0 ~ 4294967295      |
|   BIGINT    |    8     | -9223372036854775808 ~ 9223372036854775807 | 0 ~ 18446744073709551615 |

- 字符串

|   类型   |            说明             |           使用场景           |
| :------: | :-------------------------: | :--------------------------: |
|   CHAR   |     固定长度，小型数据      | 身份证号、手机号、电话、密码 |
| VARCHAR  |     可变长度，小型数据      |    姓名、地址、品牌、型号    |
|   TEXT   | 可变长度，字符个数大于 4000 |     存储小型文章或者新闻     |
| LONGTEXT |  可变长度， 极大型文本数据  |      存储极大型文本数据      |

- 时间类型

|   类型    | 字节大小 |                         示例                          |
| :-------: | :------: | :---------------------------------------------------: |
|   DATE    |    4     |                     '2020-01-01'                      |
|   TIME    |    3     |                      '12:29:59'                       |
| DATETIME  |    8     |                 '2020-01-01 12:29:59'                 |
|   YEAR    |    1     |                        '2017'                         |
| TIMESTAMP |    4     | '1970-01-01 00:00:01' UTC ~ '2038-01-01 00:00:01' UTC |

## 使用

```shell
# 登陆
mysql -uroot -p  # -u用户名， -p密码
# 退出
quit 或 exit 或 Ctrl+d 
```

### 数据库操作

```shell
# 查看所有数据库
show databases;
# 创建数据库
create database 数据库名 charset=utf8;
# 使用数据库
use 数据库名;
# 查看当前使用的数据库
select database()
# 删除数据库
drop database 数据库名;
```

### 表结构操作

```mysql
# 查看所有表
show tables;
# 创建表
create table 表名(
字段名称 数据类型 可选的约束条件,
...
);
# 查看表结构
desc 表名;
# 修改表—添加字段
alter table 表名 add 列名 类型 约束;
# 修改表—修改字段类型
alter table 表名 modify 列名 类型 约束;  # 只能修改字段类型和约束
# 修改表—修改字段名和字段类型
alter table 表名 change 原名 新名 类型及约束;
# 修改表—删除字段
alter table 表名 drop 列名;

# 删除表
drop table 表名;
```

### 表操作

#### 查询数据

```mysql
# 查询所有列
select * from 表名;
# 查询指定列
select 列1,列2... from 表名;
```

#### 添加数据

```mysql
# 全列插入：值的顺序与表结构字段的顺序完全对应
insert into 表名 values(...)
# 部分列插入
insert into 表名(列1,...) values(值1,...)
# 全列多行插入
insert into 表名 values(...),(...)...;
# 部分列多行插入
insert into 表名(列1,...) values(值1,...),(值1,...)...;
```

#### 修改数据

```mysql
update 表名 set 列1=值1,列2=值2... where 条件
```

#### 删除数据

```mysql
delete from 表名 where 条件;
```

> 上述删除操作不容易恢复，可以使用逻辑删除来代替
> `# 0表示未删除，1表示删除 alter table 表名 add isdelete bit default 0; # 逻辑删除 update 表名 set isdelete=1 where 条件`

```mysql
# 去除重复行
select distinct 列名 from 表名;
```

### where 条件查询

#### 模糊查询

```mysql
where 列名 like ''
```

- `like` 模糊查询关键字
- `%` 多个任意字符
- `_` 一个任意字符

#### 范围查询

- `between...and...` 连续范围内
- `in` 非连续范围内

#### 空判断查询

- `in null` 判断为空使用
- `is not null` 判断非空使用

> 不能使用 `=或!=` 判断为空，`null` 不是等于空字符串 `''`

### 排序

```mysql
select * from 表名 order by 列1 asc|desc ,列2 asc|desc,...;
```

- 先按第一列排序，如果第一列相通则按照第二列排序
- `asc` 升序（默认）
- `desc` 降序

### 分页

```mysql
select * from 表名 limit start, count;
```

- `start`：开始索引，默认为 0
- `count`：查询条数

### 常用变量

```mysql
# 查看当前数据库
select database();
# 查看当前用户
select user();
# 查看MySQL版本
select version();
# 查看安装路径
show variables like '%datadir%';
```

### 常用逻辑符号

```mysql
& and
|| or
^ xor
```

### 常用函数

```mysql
# 编码函数
ascii()
char()
hex()

# 文件函数
load_file()  -- 读取文件内容
```
