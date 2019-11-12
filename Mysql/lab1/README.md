# MySQL Enterprise Edition Lab1 

### Before going on.... ###
  Take a snapshot!
### Mysql Installation ###
#Run command(need super user)
```
rpm -Uhv /opt/packages/mysql/*.rpm
```
#edit(need super user)
```
vi /etc/my.cnf
```
Add this (Only in lab/test/dev Do not use on production)
```
sql_mode=''
innodb_buffer_pool_size=60M
default_authentication_plugin=mysql_native_password
```
#Restart Service MySQL(need super user)
```
service mysqld start
systemctl enable mysqld
```
#First login 
Note: A temporary password store in /var/log/mysqld.log 
```
mysql -p`grep "temporary password" /var/log/mysqld.log | awk '{ print $NF }'`
```
#Change password to Null(For easy to runing lab)
```
alter user root@'localhost' identified with mysql_native_password by '';
```
Note: It can't change. Why?  

#Change Password --> view mysql component --> remove component_validate_password'; (Only in lab/test/dev Do not use on production)
```
alter user root@'localhost' identified by 'MySQL8.0';
SELECT * FROM mysql.component;
uninstall component 'file://component_validate_password';
alter user root@'localhost' identified with mysql_native_password by '';
create user root@'%' identified with mysql_native_password by ''; grant all privileges on *.* to root@'%' with grant option;
```


