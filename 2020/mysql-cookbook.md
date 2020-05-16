[//title]: (mysql-cookbook)
[//englishtitle]: (mysql-cookbook)
[//category]: (mysql,tutorial,cookbook)
[//tags]: (mysql)
[//createtime]: (20190923)
[//updatetime]: (20200402)

## 安装

[centos 安装 mysql](https://liushiming.cn/article/how-to-install-mysql-57-on-linux-centos7/)

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

## 删除数据库中所有表

```bash
$ mysql -u [user] -p [dbname] -s -e 'show tables' | sed -e 's/^/drop table /' -e 's/$/;/' > dropalltables.sql
$ mysql -u [user] -p [dbname]  < dropalltables.sql
```

## 执行 sql 文件

```bash
mysql -u [user] -p [dbname] < /path/to/your/file.sql
```
