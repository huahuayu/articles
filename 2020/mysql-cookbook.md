[//title]: (mysql-cookbook)
[//englishtitle]: (mysql-cookbook)
[//category]: (mysql,tutorial,cookbook)
[//tags]: (mysql)
[//createtime]: (20190923)
[//updatetime]: (20201224)

## 安装

[centos 安装 mysql](https://liushiming.cn/article/how-to-install-mysql-57-on-linux-centos7/)

## docker 安装

docker 安装很方便：

```bash
sudo docker run -d --restart always -p 3306:3306 --name mysql5.7 -v /var/lib/mysql:/var/lib/mysql -e MYSQL_ROOT_HOST=% -e MYSQL_ROOT_PASSWORD=root mysql:5.7
```

- restart always: 运行 docker 时自动拉起容器
- MYSQL_ROOT_HOST=%：允许 root 远程连接
- -v /var/lib/mysql:/var/lib/mysql: 前一个是宿主机目录，后一个容器目录，将 mysql 数据目录映射到宿主机

建议将数据目录映射出来。

## 登录

```bash
$ mysql -u [user] -p
```

## 查看数据库

```sql
show databases;
```

## 选择数据库

```sql
use [database];
```

## 查看用户列表

```sql
select user from mysql.user;
```

## 新建用户

```sql
CREATE USER '[user]'@'[host]' IDENTIFIED BY '[password]';
```

允许从指定 host 连接

```sql
CREATE USER '[user]'@'localhost' IDENTIFIED BY '[password]';
```

允许从任意 host 连接

```sql
CREATE USER '[user]'@'%' IDENTIFIED BY '[password]';
```

## 用户授权

最常用的权限有：

- ALL PRIVILEGES – 所有权限
- CREATE – 新建数据库/表
- DROP - 删除数据库/表
- DELETE - 删除数据表的记录
- INSERT - 插入数据记录
- SELECT – 查询表数据
- UPDATE - 更新表数据

赋权语句：

```sql
GRANT [permission1], [permission2] ON [database].[table] TO '[user]'@'[host]';
```

示例：  
将某数据库的所有权限赋给用户

```sql
GRANT ALL PRIVILEGES ON database_name.* TO 'database_user'@'localhost';
```

将所有数据库的所有权限赋给用户

```sql
GRANT ALL PRIVILEGES ON *.* TO 'database_user'@'localhost';
```

将数据库中的某给表的权限赋给用户

```sql
GRANT ALL PRIVILEGES ON database_name.table_name TO 'database_user'@'localhost';
```

将某数据库的 select，insert，delete 权限赋给用户

```sql
GRANT SELECT, INSERT, DELETE ON database_name.* TO database_user@'localhost';
```

## 查询用户权限

```sql
SHOW GRANTS FOR '[user]'@'[host]';
```

## 吊销权限

```sql
REVOKE ALL PRIVILEGES ON [database].* FROM '[user]'@'[host]';
```

## 查看运行的端口

```sql
SHOW GLOBAL VARIABLES LIKE 'PORT';
```

也可以使用 netstat 命令查看

```bash
netstat -lntup | grep mysql
```

## 修改密码

```bash
$ mysql -u root -p
Enter password：***
mysql>use mysql;
Database changed
mysql> update user set authentication_string=PASSWORD("your_password") where user='your_user';
mysql> FLUSH PRIVILEGES;
mysql> quit;
```

## 免密登录

```bash
vi /etc/my.cnf
```

在`[mysqld]`一栏下面加上一行

```text
skip-grant-tables
```

重启 mysqld 服务

```bash
systemctl restart mysqld
```

命令行敲`mysql`即可登录

## 重置 root 密码

免密登录后可以重置用户密码

```sql
update user set authentication_string=PASSWORD("your_password") where user='your_user';
```

## 删除用户

```sql
DROP USER 'user'@'localhost';
```

## 允许 root 远程访问

授权

```sql
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'your_password' WITH GRANT OPTION;
```

刷新权限

```sql
flush privileges;
```

## 导出数据库

```bash
$ mysqldump -u [user] -p [dbname] > /path/to/database-dump.sql
```

## 导入数据库

```bash
$ mysql -u [user] -p [new_dbname] < /path/to/database-dump.sql
```

## 导出所有数据

```bash
$ mysqldump -u [user] -p --all-databases > /path/to/alldata.sql
```

## 导入所有数据

```bash
$ mysql -u [user] -p < /path/to/alldata.sql
```

## 删除数据库(保留 database)

```bash
$ mysql -u [user] -p [dbname] -s -e 'show tables' | sed -e 's/^/drop table /' -e 's/$/;/' > dropalltables.sql
$ mysql -u [user] -p [dbname]  < dropalltables.sql
```

## 清空数据库表(保留 table 结构)

第一步：执行以下 sql（替换 mydatabase 为你要清空的数据库）

```sql
select CONCAT('TRUNCATE TABLE ',TABLE_SCHEMA,'.',table_name,';') from information_schema.tables where TABLE_SCHEMA='mydatabase';
```

第二步：再执行以上 sql 的输出结果就好

参考：https://wiki.jikexueyuan.com/project/sql/truncate-table.html

## 执行 sql 文件

```bash
mysql -u [user] -p [dbname] < /path/to/your/file.sql
```

## 连接远程服务器

```bash
mysql -u [user] -p -h [server_address] -P [mysql_port] -D [dbname]
```
