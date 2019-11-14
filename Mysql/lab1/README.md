# MySQL Enterprise Edition Lab1 

## Before going on.... ##
  #Take a snapshot!#
## Mysql Installation ##
### Run command ###
need super user
```
rpm -Uhv /opt/packages/mysql/*.rpm
rpm -Uhv /opt/packages/shell/*.rpm
cd /opt/download/mysql/mysql8.0/tar
tar -zxvf *.* 
```
### Edit MySQL Configuration files ###
need super user
```
vi /etc/my.cnf
```
#### Add this ####
Note: Change mode vi to insert, Key(i)
Only in lab/test/dev Do not use on production
```
sql_mode=''
innodb_buffer_pool_size=60M
default_authentication_plugin=mysql_native_password
```
Note: Exist from vi, Key(Esc) --> Key(:wq) --> Key(Enter)  
### Restart Service MySQL ###
need super user
```
service mysqld start
systemctl enable mysqld
```
## First login
Note: A temporary password store in /var/log/mysqld.log 
```
mysql -p`grep "temporary password" /var/log/mysqld.log | awk '{ print $NF }'`
```
## Show Database;
```
mysql>
```
```
show databases;
```
ERROR 1820 (HY000): You must reset your password using ALTER USER statement before executing this statement.
What happens?

## Change password to Null
Only in lab/test/dev Do not use on production
```
alter user root@'localhost' identified with mysql_native_password by '';
```
Note: It can't change. Why?  

## Change password to Null
Change Password --> view mysql component --> remove component_validate_password'; 
```
alter user root@'localhost' identified by 'MySQL8.0';
SELECT * FROM mysql.component;
uninstall component 'file://component_validate_password';
alter user root@'localhost' identified with mysql_native_password by '';
create user root@'%' identified with mysql_native_password by ''; grant all privileges on *.* to root@'%' with grant option;
flush privileges;
```
## Show Database;
```
show databases;
\q
```

## Create new MySQL Instance
```
mkdir /lab
cd /lab
ln -s /opt/download/mysql/mysql8.0/tar/mysql-commercial-8.0.18-el7-x86_64 mysql
chown mysql.mysql /lab
sudo -H -u mysql bash -c '/usr/sbin/mysqld --initialize-insecure --basedir=/lab/mysql/bin/ --datadir=/lab/mysql_home01'
``` 
### Create mysql configure files 
```
cat << EOF > /lab/mysql_home01/my.cnf
[mysqld]

sql_mode=''
innodb_buffer_pool_size=60M
default_authentication_plugin=mysql_native_password

# server configuration
datadir=/lab/mysql_home01/
basedir=/lab/mysql/bin/
plugin_dir=/usr/lib64/mysql/plugin/
lc-messages-dir=/usr/share/mysql-8.0/

log-error=/lab/mysql_home01/mysqld.log
pid-file=/lab/mysql_home01/mysqld.pid
port=3310
socket=/lab/mysql_home01/mysqld.sock
EOF
``` 


