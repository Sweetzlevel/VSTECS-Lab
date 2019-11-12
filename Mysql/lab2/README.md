# MySQL Enterprise Edition Lab2
#Introduction MySQL 
## MySQL Innovation: 5.7 --> 8.0 
![](img/01.PNG) 
## MySQL History 
![](img/02.PNG)

# MySQLComponents
## Create new MySQL Instance
```
mkdir /lab
chown mysql.mysql /lab
sudo -H -u mysql bash -c '/usr/sbin/mysqld --initialize-insecure --basedir=/usr/sbin/ --datadir=/lab/mysql_home01'
``` 
## Create mysql configure files 
```
cat << EOF > /lab/mysql_home01/my.cfg
[mysqld]

sql_mode=''

# server configuration
datadir=/lab/mysql_home01/
basedir=/usr/sbin/
plugin_dir=/usr/lib64/mysql/plugin/
lc-messages-dir=/usr/share/mysql-8.0/

port=3310
socket=/lab/mysql_home01/s1.sock
EOF
``` 


## MySQL Server 
```
sudo -u mysql mysqld --defaults-file=/lab/mysql_home01/my.cfg 2>&1 &>/dev/null &
```
### Connect to mysqlserver 
```
mysql -u root -h 127.0.0.1 -P3310

```




###Started with mysqld



###Started with mysqld_safe

###Started with mysqld_multi

##Storage Engines
