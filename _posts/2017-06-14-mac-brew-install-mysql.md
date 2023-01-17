---
layout: post
title:  Mac下使用Homebrew安装Mysql
date:   2017-06-14 15:45:17
description: 
tags: mysql
categories: devops
---

测试操作系统为：macOS Sierra 10.12.4
## 关于Homebrew
[Homebrew](http://brew.sh/) 简称brew，是Mac OSX上的软件包管理工具，能在Mac中方便的安装软件或者卸载软件，相当于Linux下的apt-get、yum等软件包管理工具。
## 安装Homebrew
Homebrew的安装非常简单，打开终端，执行下列命令即可：
 ` $ /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)" `


## 安装Mysql
```
$ brew install mysql 
==> Installing dependencies for mysql: openssl
==> Installing mysql dependency: openssl
==> Downloading https://homebrew.bintray.com/bottles/openssl-1.0.2l.sierra.bottle.tar.gz
######################################################################## 100.0%
==> Pouring openssl-1.0.2l.sierra.bottle.tar.gz
==> Using the sandbox
==> Caveats
A CA file has been bootstrapped using certificates from the SystemRoots
keychain. To add additional certificates (e.g. the certificates added in
the System keychain), place .pem files in
  /usr/local/etc/openssl/certs

and run
  /usr/local/opt/openssl/bin/c_rehash

This formula is keg-only, which means it was not symlinked into /usr/local,
because Apple has deprecated use of OpenSSL in favor of its own TLS and crypto libraries.

If you need to have this software first in your PATH run:
  echo 'export PATH="/usr/local/opt/openssl/bin:$PATH"' >> ~/.bash_profile

For compilers to find this software you may need to set:
    LDFLAGS:  -L/usr/local/opt/openssl/lib
    CPPFLAGS: -I/usr/local/opt/openssl/include

==> Summary
🍺  /usr/local/Cellar/openssl/1.0.2l: 1,709 files, 12.2MB
==> Installing mysql
==> Downloading https://homebrew.bintray.com/bottles/mysql-5.7.18_1.sierra.bottle.tar.gz
######################################################################## 100.0%
==> Pouring mysql-5.7.18_1.sierra.bottle.tar.gz
==> /usr/local/Cellar/mysql/5.7.18_1/bin/mysqld --initialize-insecure --user=yueqi --basedir=/usr/local/Cellar/mysql/5.7.18_1 --datadir=/usr/local/var/mysql --tmpdir=/tmp
==> Caveats
We've installed your MySQL database without a root password. To secure it run:
    mysql_secure_installation

MySQL is configured to only allow connections from localhost by default

To connect run:
    mysql -uroot

To have launchd start mysql now and restart at login:
  brew services start mysql
Or, if you don't want/need a background service you can just run:
  mysql.server start
==> Summary
🍺  /usr/local/Cellar/mysql/5.7.18_1: 321 files, 232.9MB
```
## 启动mysql
安装完并不会默认启动，这时输入mysql命令会出现一下错误。
```
$ mysql 
ERROR 2002 (HY000): Can't connect to local MySQL server through socket '/tmp/mysql.sock' (2)
```
启动mysql的命令如下：
```
$ brew services start mysql
```
或者
```
$ mysql.server start
```

## 安全配置mysql
安装完root用户没有设密码，很不安全，需要配置root用户密码。
并且最好要限制root用户远程访问，删除无名用户和测试库。

```
$  mysql_secure_installation

Securing the MySQL server deployment.

Connecting to MySQL using a blank password.

VALIDATE PASSWORD PLUGIN can be used to test passwords
and improve security. It checks the strength of password
and allows the users to set only those passwords which are
secure enough. Would you like to setup VALIDATE PASSWORD plugin?

Press y|Y for Yes, any other key for No: y

There are three levels of password validation policy:

LOW    Length >= 8
MEDIUM Length >= 8, numeric, mixed case, and special characters
STRONG Length >= 8, numeric, mixed case, special characters and dictionary                  file

Please enter 0 = LOW, 1 = MEDIUM and 2 = STRONG: 0
Please set the password for root here.

New password: 

Re-enter new password: 

Estimated strength of the password: 50 
Do you wish to continue with the password provided?(Press y|Y for Yes, any other key for No) : y
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

Reloading the privilege tables will ensure that all changes
made so far will take effect immediately.

Reload privilege tables now? (Press y|Y for Yes, any other key for No) : y
Success.

All done! 
```

