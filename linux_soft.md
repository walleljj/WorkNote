## 一、Mysql

> MySql下载地址：` https://dev.mysql.com/downloads/mysql/`  [官网下载](https://dev.mysql.com/downloads/mysql/)

### 1.安装之前的检查安装和卸载

```shell
rpm -qa | grep mysql		#检测系统是否安装 MySQL
service mysqld status		#查看Mysql服务运行状态
service mysqld stop 
service mysql stop			#停止MySQL服务
rpm -e --nodeps mysql		#卸载Mysql
find / -name mysql			#find命令查询：速度会慢点
whereis mysql				#whereis命令搜索，速度较快
rm -rf  /usr/lib64/mysql/
rm -rf  /usr/share/mysql	#删除相关配置文件
id mysql					#查看mysql的用户和用户组
userdel mysql				#删除mysql的用户和用户组
#至此MySQL的卸载完成，再次指向检查命令查看是否有mysql
```

### 2.下载安装

```shell
#安装文件下载目录：/data/software
#Mysql目录安装位置：/usr/local/mysql
#数据库保存位置：/data/mysql
#日志保存位置：/data/log/mysql

#下载解压mysql安装包
wget https://downloads.mysql.com/archives/get/p/23/file/mysql-5.7.20-linux-glibc2.12-x86_64.tar.gz
tar -zxvf mysql-5.7.20-linux-glibc2.12-x86_64.tar.gz
mv mysql-5.7.20-linux-glibc2.12-x86_64	/usr/local/mysql
#创建mysql的用户组和用户
group add mysql
useradd -r -g mysql mysql
#####使用mysql用户
mkdir -p  /data/mysql              #创建目录
chown mysql:mysql -R /data/mysql   #赋予权限

```

### 3.修改配置

```shell
#修改配置文件
vi /etc/my.config
[mysqld]
bind-address=0.0.0.0
port=3306
user=mysql
basedir=/usr/local/soft/mysql  # mysql安装目录
datadir=/data/mysql  # 数据存放目录
socket=/tmp/mysql.sock
log-error=/data/mysql/mysql.err
pid-file=/data/mysql/mysql.pid
#character config
character_set_server=utf8mb4
symbolic-links=0
explicit_defaults_for_timestamp=true
```

```shell
#初始化数据库
#进入mysql的bin目录
./mysqld --defaults-file=/etc/my.cnf --basedir=/usr/local/soft/mysql/ --datadir=/data/mysql/ --user=mysql --initialize
```

```shell
#启动mysql
#注意先将mysql.server放置到/etc/init.d/mysql中，可以让dameon来管理Mysql的启动(即也就是service，CentOS7就是syetemctl)
cp /usr/local/soft/mysql/support-files/mysql.server /etc/init.d/mysql
service mysql start
```

### 4.修改数据库密码

```shell
./mysql -uroot -p临时密码
```

​		在数据库中接着执行以下三行代码：

```shell
SET PASSWORD = PASSWORD('123456');
ALTER USER 'root'@'localhost' PASSWORD EXPIRE NEVER;
FLUSH PRIVILEGES; 
```

​		这样数据库密码就修改成功了。但是此时你如果远程连接数据库，你会发现无法联通，这是正常现象，因为你还没有开放访问IP端口。

​		开放访问IP端口。先进入到数据库，接着执行以下三行代码，这样就开放了数据库访问IP端口。

```shell
use mysql;     #访问mysql库
update user set host = '%' where user = 'root';   #使root用户能在任何IP进行访问
FLUSH PRIVILEGES;
```

### 5.忘记密码解决

- 修改MySQL的登录设置

```shell
vim /etc/my.cnf
#在[mysqld]的段中加上一句：skip-grant-tables
[mysqld]
datadir=/var/lib/mysql
socket=/var/lib/mysql/mysql.sock
skip-grant-tables
```

- 重新启动mysql

```shell
service mysqld restart
```

- 登录mysql并修改mysql密码

```shell
# mysql
Welcome to the MySQL monitor. Commands end with ; or \g.
Your MySQL connection id is 3 to server version: 3.23.56
Type 'help;' or '\h' for help. Type '\c' to clear the buffer.
mysql> USE mysql ;
Database changed
mysql> UPDATE user SET Password = password ( 'new-password' ) WHERE User = 'root' ;
Query OK, 0 rows affected (0.00 sec)
Rows matched: 2 Changed: 0 Warnings: 0
mysql> flush privileges ;
Query OK, 0 rows affected (0.01 sec)
mysql> quit
```

- 重新修改my.cnf配置文件
- 重启mysql

## 二、Redis

> Redis下载地址：` https://redis.io/download` [官网下载](https://redis.io/download)

### 1.下载解压

```shell
wget http://download.redis.io/releases/redis-6.0.8.tar.gz
tar -xvf redis-6.0.8.tar.gz
mv  redis-6.0.8   /usr/local/redis
```

### 2.安装环境

```shell
gcc -v		#查看gcc版本
#yum install gcc-c++		#安装gcc
###升级GCC环境为9版本
yum  -y  install  centos-release-scl
yum  -y  install  devtoolset-9-gcc  devtoolset-9-gcc-c++  devtoolset-9-binutils
```

### 3.安装Redis

```shell
#在Redis解压后的目录下
make										#编译
make  install  PREFIX=/usr/local/redis-6.x	#安装到指定目录
```

### 4.配置Redis

```shell
#在Redis的安装目录的bin下
mkdir conf
#将解压的redis配置文件拷贝到安装目录
cp /usr/local/redis6.x/redis-6.0.8/redis.conf /usr/local/redis6.x/bin/conf/
```

### 5.启动Redis测试

```sh
./redis-server conf/redis.conf
#新窗口连接测试
./redis-cli -p  6379
ping
127.0.0.1:6379> shutdown
exit　　#### 退出redis
```

### 6.配置为后台启动

```shell
vim   /usr/local/redis6.x/bin/conf/redis.conf
set nu 显示行号
将daemonize no 改成daemonize yes（表示开启redis后台服务：约225行）
```



