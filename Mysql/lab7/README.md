# MySQL Enterprise Edition Lab7
MySQL Enterprise Monitor

# Preparation
Create new Server --> uxnode1
                    -->ip add 10.1.x.201

# Server [uxmaster] 
use existing node
```
ssh uxnode1 
rpm -Uhv /opt/packages/mysql/*.rpm

```

### Edit MySQL Configuration files ###
need super user
```
vi /etc/my.cnf
```
#### Add this ####
Note: Change mode vi to insert, Key(i)
```
# Only in lab/test/dev Do not use on production
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
## Change password to Null
Change Password --> view mysql component --> remove component_validate_password'; 
```
alter user root@'localhost' identified by 'MySQL8.0';
SELECT * FROM mysql.component;
uninstall component 'file://component_validate_password';
alter user root@'localhost' identified with mysql_native_password by '';
create user root@'%' identified with mysql_native_password by ''; grant all privileges on *.* to root@'%' with grant option;
flush privileges;
\q
```


Next: [ MySQL Enterprise Encryption](../lab8) 