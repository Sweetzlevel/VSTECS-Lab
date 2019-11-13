# MySQL Enterprise Edition Lab4
MySQL Enterprise Backup

# Preparation
|  |Pass a lab in below |
| ------------- | ------------- |
[Lab2](lab2) | Introduction MySQL  

or 

## Create new MySQL Instance
Do not do this if pass lab 2
```
cd /opt/download/mysql/mysql8.0/tar
tar -zxvf *.* 
mkdir /lab
cd /lab
ln -s /opt/download/mysql/mysql8.0/tar/mysql-commercial-8.0.18-el7-x86_64 mysql
chown mysql.mysql /lab
sudo -H -u mysql bash -c '/usr/sbin/mysqld --initialize-insecure --basedir=/lab/mysql/bin/ --datadir=/lab/mysql_home01'
``` 
### Create mysql configure files 
Do not do this if pass lab 2
```
cat << EOF > /lab/mysql_home01/my.cfg
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

### Started with mysqld_safe
```
sudo -u mysql /lab/mysql/bin/mysqld_safe --defaults-file=/lab/mysql_home01/my.cfg 2>&1 &>/dev/null &
```
### Connect to mysqlserver 

```
mysql -u root -S /lab/mysql_home01/mysqld.sock
```