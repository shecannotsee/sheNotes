在mac使用mysql

### 0.安装mysql

```bash
brew install mysql

sudo apt install mysql-server # ubuntu 20.04
```

### 1.启动mysql

```bash
mysql.server start
mysql.server stop
mysql.server status
```

### 2.连接数据库

安装后默认用户名为root，无密码

```bash
# host:数据库ip
# user:数据库用户
mysql -h host -u user -p
# 本机可使用
# user:数据库用户
mysql -u user -p
```

### 3.使用数据库

#### 1.数据库相关

```mysql
# 查看数据库版本以及创建时间(可见是通过select进行数据库系统信息的查询)
mysql> SELECT VERSION(), CURRENT_DATE
# 查看所有数据库
mysql>show databases;
# 创建数据库
# menagerie:可改为任意数据库名
mysql>CREATE DATABASE menagerie;
# 切换到任意数据库，后续进行其他操作
# menagerie:可改为任意数据库名
mysql> USE menagerie
```

#### 2.表相关

```mysql
# 查看所有表
mysql> SHOW TABLES;
# 创建表
mysql> CREATE TABLE pet (name VARCHAR(20), owner VARCHAR(20),
       species VARCHAR(20), sex CHAR(1), birth DATE, death DATE);
# 查看表中列的详细信息
mysql> DESCRIBE pet;

# 向创建好的表中插入数据(insert语句)
mysql> INSERT INTO pet
       VALUES ('Puffball','Diane','hamster','f','1999-03-30',NULL);
# 查询(select语句)
mysql> SELECT * FROM pet;
# 更新(update语句)
mysql> UPDATE pet SET birth = '1989-08-31' WHERE name = 'Bowser';
# 删除(delete语句)
mysql> DELETE FROM pet;
```

## c connect

去官网下载对应mysql版本的api版本，选好平台版本解压打开就有include，lib和bin



## 关于一些mysql的配置

#### 1.不再本地环回，可让远程主机连接

```bash
sudo vim /etc/mysql/mysql.conf.d/mysqld.cnf
```

将bind-address = 127.0.0.1注销

#### 2.端口问题

```bash
sudo iptables -I INPUT -p tcp --dport 8080 -j ACCEPT//开放8080端口
```

#### 3.mysql配置问题

```bash
mysql>use mysql;
mysql> UPDATE user SET host='%' WHERE User='root';//设置任何主机均可访问
mysql> UPDATE user SET plugin='mysql_native_password' WHERE User='root';//设置可以通过密码的方式连接
mysql> FLUSH PRIVILEGES;//应用更改
```

