# 在CentOS上安装mysql8
## 安装mysql8源
###### 检查是否安装过 MySQL
> rpm -qa | grep mysql
###### 下载MySQL源
> wget http://dev.mysql.com/get/mysql80-community-release-el7-10.noarch.rpm
###### 安装MySQL源
> yum localinstall mysql80-community-release-el7-10.noarch.rpm
###### 检查MySQL源是否安装成功
> yum repolist enabled | grep "mysql.*-community.*"
## 安装mysql8
###### 通过已经安装好的MySQL源安装MySQL
> yum install mysql-community-server
###### 查看MySQL是否安装成功
> yum list installed mysql-*
## 启动MySQL
> systemctl start mysqld
###### 查看MySQL服务状态，active(running)为已启动 
> systemctl status mysqld
###### 获取MySQL临时登录密码 
> grep 'temporary password' /var/log/mysqld.log
###### 以root用户用密码方式登录MySQL，后输入临时密码
> mysql -u root -p 
###### 设置新密码。MySQL 默认的密码复杂度为 MEDIUM，所以新密码至少为 8 位，并且必须包含大、小写字母、数字和特殊字符。
> (MySQL) -> ALTER USER 'root'@'localhost' IDENTIFIED BY '新密码'; 
###### 将所有数据库的所有表（*.*）允许以root用户远程访问，远程登录时使用的密码也同时设置。@'%'为允许所有IP。
> (MySQL) -> grant all privileges on *.* to 'root' @'%' identified by '远程登录密码'; 
###### 刷新权限，使配置立即生效。
> (MySQL) -> flush privileges; 
## mysql版本8，在给新用户授权时，发生了变化
- create user 'root'@'localhost' identified by  'password';
- create user 'root'@'%' identified by 'passwd';
- grant all privileges on *.* to 'root'@'localhost' with grant option;
- grant all privileges on *.* to 'root'@'%' with grant option;
- flush privileges;
## 配置防火墙
- systemctl status firewalld # 查看防火墙状态，active(running)为已启动
- firewall-cmd --zone=public --add-service=mysql --permanent # 将MySQL添加到防火墙允许列表
- firewall-cmd --reload # 重载防火墙设置
- firewall-cmd --zone=public --list-services # 查看防火墙允许的服务列表