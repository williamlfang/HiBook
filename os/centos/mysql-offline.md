# 无网络环境

> **[info]**离线安装 MySQL
> 因需要在券商机房部署服务器，无法在联网情况下进行安装程序，所以选择将需要安装的程序包下载到本地，然后从源代码进行编译安装。离线安装 `MySQL` 稍微有点复杂，过程会涉及到动态链接库、boost 依赖、权限设置等诸多问题。因此，我选择以`rpm`的形式进行安装。

> **[sucess]**参考这篇博客[centos7 离线安装mysql-5.7.16 - zzshine的专栏 - csdn博客](https://blog.csdn.net/zz657114506/article/details/53553845)进行安装。

# 下载 MySQL 安装包

```bash
mkdir ~/mysql; cd ~/mysql
wget http://dev.mysql.com/get/Downloads/MySQL-5.7/mysql-5.7.13-1.el7.x86_64.rpm-bundle.tar

## 安装mysql-community-server需要安装libaio
wget http://mirror.centos.org/centos/6/os/x86_64/Packages/libaio-0.3.107-10.el6.x86_64.rpm
```

下载完成后，解压获得几个独立的安装包
```bash
[trader@localhost mysql]$ tar xvf mysql-5.7.13-1.el7.x86_64.rpm-bundle.tar

[trader@localhost mysql]$ ll -alh
total 1.1G
drwxrwxr-x  2 trader trader 4.0K Nov 14 17:57 .
drwx------ 12 trader trader  286 Nov 15 09:26 ..
-rw-rw-r--  1 trader trader 542M Nov 14 17:57 mysql-5.7.13-1.el7.x86_64.rpm-bundle.tar
-rw-r--r--  1 trader trader  24M May 26  2016 mysql-community-client-5.7.13-1.el7.x86_64.rpm
-rw-r--r--  1 trader trader 271K May 26  2016 mysql-community-common-5.7.13-1.el7.x86_64.rpm
-rw-r--r--  1 trader trader 3.7M May 26  2016 mysql-community-devel-5.7.13-1.el7.x86_64.rpm
-rw-r--r--  1 trader trader  44M May 26  2016 mysql-community-embedded-5.7.13-1.el7.x86_64.rpm
-rw-r--r--  1 trader trader  23M May 26  2016 mysql-community-embedded-compat-5.7.13-1.el7.x86_64.rpm
-rw-r--r--  1 trader trader 120M May 26  2016 mysql-community-embedded-devel-5.7.13-1.el7.x86_64.rpm
-rw-r--r--  1 trader trader 2.2M May 26  2016 mysql-community-libs-5.7.13-1.el7.x86_64.rpm
-rw-r--r--  1 trader trader 2.1M May 26  2016 mysql-community-libs-compat-5.7.13-1.el7.x86_64.rpm
-rw-r--r--  1 trader trader  49M May 26  2016 mysql-community-minimal-debuginfo-5.7.13-1.el7.x86_64.rpm
-rw-r--r--  1 trader trader 152M May 26  2016 mysql-community-server-5.7.13-1.el7.x86_64.rpm
-rw-r--r--  1 trader trader  14M May 26  2016 mysql-community-server-minimal-5.7.13-1.el7.x86_64.rpm
-rw-r--r--  1 trader trader 111M May 26  2016 mysql-community-test-5.7.13-1.el7.x86_64.rpm
```

这些包涉及到各种依赖关系，接下来，我们需要安装一定的顺序进行安装这些 `rpm` 程序包。

1. `mysql-community-common`: 是共用的基础包
2. `mysql-community-libs`: 是常用的动态链接库
3. `mysql-community-client`: 客户端软件
4. `mysql-community-server`: 服务器软件
5. `mysql-community-libs-compat`: 兼容性依赖包，用于解决 `libmysqlclient.so.18` 找不到的问题

# 完全清理 MySQL 旧版本

## 卸载

```bash
rpm -qa|grep mariadb

yum -y remove mysql-community-client-5.7.13-1.el7.x86_64
yum -y remove perl-DBD-MySQL-4.023-6.el7.x86_64
yum remove -y mysql-community-client-5.7.13-1.el7.x86_64 mysql-community-server-8.0.18-1.el7.x86_64
yum remove -y mysql-community-libs-5.7.13-1.el7.x86_64 mysql-community-common-5.7.13-1.el7.x86_64
```

## 清理

```bash
rm /usr/lib64/mysql/libmysqlclient.so.18
rm -rf /var/lib/mysql/
rm -rf /usr/mysql/
rm -rf /usr/share/mysql
rm -rf /etc/my.cnf
rm -rf /var/log/mysqld.log  ## 如果不删除，会影响新安装的 MySQL 无法写入密码 
```

# 安装 MySQL5.7

## 执行安装

按照以下步骤安装即可

```bash
sudo rpm -ivh mysql-community-common-5.7.13-1.el7.x86_64.rpm
sudo rpm -ivh mysql-community-libs-5.7.13-1.el7.x86_64.rpm
sudo rpm -ivh mysql-community-client-5.7.13-1.el7.x86_64.rpm
sudo rpm -ivh libaio-0.3.107-10.el6.x86_64.rpm
sudo rpm -ivh mysql-community-server-5.7.13-1.el7.x86_64.rpm
sudo rpm -ivh mysql-community-libs-compat-5.7.13-1.el7.x86_64.rpm
```

## 配置数据库

### 初始化

指定datadir, 执行后会生成~/.mysql_secret密码文件

```bash
sudo mysql_install_db --datadir=/var/lib/mysql
```

### 更改权限

更改mysql数据库目录的所属用户及其所属组，并启动mysql数据库

```bash
sudo chown mysql:mysql /var/lib/mysql -R
sudo systemctl start mysqld
```

### root 登录

 password 通过 `cat ~/.mysql_secret` 命令可以查看初始密码

```bash
cat ~/.mysql_secret

mysql -uroot -p
Enter password: 

mysql> set password=password('*********');
```

### 设置开机启动

```bash
## 检查是否已经是开机启动
systemctl list-unit-files | grep mysqld
mysqld.service                                enabled 
mysqld@.service                               disabled

## 开机启动
systemctl enable mysqld.service
```

至此，便完成了 `MySQL` 离线安装的全部过程。

# 问题

## Python 调用 MySQLdb 报错

> **[danger]** MySQLdb 找不到 libmysqlclient.so.18:
>
> raceback (most recent call last):
> File “mysqlshell.py”, line 1, in
> import MySQLdb
> File “/usr/lib/python2.7/site-packages/MySQLdb/**init**.py”, line 19, in
> import _mysql
> ImportError: libmysqlclient.so.18: cannot open shared object file: No such file or directory

上面有提示，需要安装 `mysql-community-libs-compat-5.7.13-1.el7.x86_64.rpm`，然后看一下 `libmysqlclient.so.18` 的路径。

```bash
locate libmysql
/usr/lib64/mysql/libmysqlclient.so.18
/usr/lib64/mysql/libmysqlclient.so.18.1.0
/usr/lib64/mysql/libmysqlclient.so.20
/usr/lib64/mysql/libmysqlclient.so.20.3.0
/usr/lib64/mysql/libmysqlclient_r.so.18
/usr/lib64/mysql/libmysqlclient_r.so.18.1.0
```

需要把以上的路径添加到环境路径

```bash
export LD_LIBRARY_PATH=/usr/lib64/mysql:$LD_LIBRARY_PATH
```

```bash
[trader@localhost ~]$ python
Python 3.7.1 (default, Dec 14 2018, 19:28:38)
[GCC 7.3.0] :: Anaconda, Inc. on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> import MySQLdb
>>>
```

