# MySQL Enterprise Edition Lab3
MySQL Workbench

# Preparation
|  |Pass a lab in below |
| ------------- | ------------- |
[Lab2](lab2) | Introduction MySQL  

or 

## Create new MySQL Instance
```
cd /opt/download/mysql/mysql8.0/tar
tar -zxvf *.* 
mkdir /lab
cd /lab
ln -s /opt/download/mysql/mysql8.0/tar/mysql-commercial-8.0.18-el7-x86_64 mysql
chown mysql.mysql /lab
sudo -H -u mysql bash -c '/usr/sbin/mysqld --initialize-insecure --basedir=/lab/mysql/bin/ --datadir=/lab/mysql_home01'
``` 
## Create mysql configure files 
```
cat << EOF > /lab/mysql_home01/my.cfg
[mysqld]

sql_mode=''

# server configuration
datadir=/lab/mysql_home01/
basedir=/lab/mysql/bin/
plugin_dir=/usr/lib64/mysql/plugin/
lc-messages-dir=/usr/share/mysql-8.0/

port=3310
socket=/lab/mysql_home01/s1.sock
EOF
``` 
