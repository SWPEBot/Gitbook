# Install MySQL Server

## Install MySQL Server Package

```bash
sudo apt-get update
sudo apt-get install mysql-server
```

## Configuration MySQL Server

```bash
sudo mysql_secure_installation
```

Example:

```bash
sysadmin@swpe-xeon-server:~$ sudo mysql_secure_installation

Securing the MySQL server deployment.

Connecting to MySQL using a blank password.

VALIDATE PASSWORD PLUGIN can be used to test passwords
and improve security. It checks the strength of password
and allows the users to set only those passwords which are
secure enough. Would you like to setup VALIDATE PASSWORD plugin?

Press y|Y for Yes, any other key for No: n

Please set the password for root here.

New password:

Re-enter new password:

By default, a MySQL installation has an anonymous user,
allowing anyone to log into MySQL without having to have
a user account created for them. This is intended only for
testing, and to make the installation go a bit smoother.
You should remove them before moving into a production
environment.

Remove anonymous users? (Press y|Y for Yes, any other key for No) : y
Success.

Normally, root should only be allowed to connect from
'localhost'. This ensures that someone cannot guess at
the root password from the network.

Disallow root login remotely? (Press y|Y for Yes, any other key for No) : y
Success.

By default, MySQL comes with a database named 'test' that
anyone can access. This is also intended only for testing,
and should be removed before moving into a production
environment.

Remove test database and access to it? (Press y|Y for Yes, any other key for No) : y
 - Dropping test database...
Success.

 - Removing privileges on test database...
Success.
```

## Check mysql service status

```bash
systemctl status mysql.service
```

## 配置远程访问

[参考链接](https://wiki.ubuntu.org.cn/MySQL%E5%AE%89%E8%A3%85%E6%8C%87%E5%8D%97)

```bash
# 首先用根用户进入
sudo mysql -uroot -p

# 打开mysql配置文件
# 注意：不同 mysql 版本此配置文件位置和名字可能不同，如果无以下配置，那么您的 mysql 版本与本文不同，请查阅相应版本的配置。
sudo vim /etc/mysql/mysql.conf.d/mysqld.cnf
#找到将bind-address = 127.0.0.1注销​
#bind-address            = 127.0.0.1
bind-address            = 0.0.0.0

# 修改后，重启MySQL服务器
sudo /etc/init.d/mysql restart

# 进入 MySQL 程序修改 root 账户的远程访问的权限
sudo mysql -u root -p

# 刷新权限
flush privileges;

# 重新启动服务器
systemctl restart mysql.service

完成 MySQL 的安装与远程访问设置

# 登录root用户
mysql -u root -p
# 输入密码登录

# 新建用户
CREATE USER swpe IDENTIFIED BY 'sysadmin';

# 新建 DATABASE
CREATE DATABASE devops;

# 删除数据库
drop database devops;

# 用户授权
grant select,insert,update,delete on *.* to 'swpe'@'%';

给来自所有地址的用户swpe分配可对所有数据库所有表进行create,select,insert,update,delete,create,drop等操作的权限
```

## 问题解决

[为什么 MYSQL 不用密码也能访问？](http://www.r9it.com/20180810/why-mysql-nopass-login.html)
