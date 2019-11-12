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
Add this (Useing in Lab/test/dev Not use this in production)
```
sql_mode=''
innodb_buffer_pool_size=60M
default_authentication_plugin=mysql_native_password
```
